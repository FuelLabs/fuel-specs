# Fuel VM Specification

- [Introduction](#introduction)
- [Parameters](#parameters)
- [Semantics](#semantics)
- [Flags](#flags)
- [Instruction Set](#instruction-set)
- [VM Initialization](#vm-initialization)
- [Contexts](#contexts)
- [Predicate Estmation](#predicate-estimation)
- [Predicate Verification](#predicate-verification)
- [Script Execution](#script-execution)
- [Call Frames](#call-frames)
- [Ownership](#ownership)

## Introduction

This document provides the specification for the Fuel Virtual Machine (FuelVM). The specification covers the types, instruction set, and execution semantics.

## Parameters

| name                    | type     | value   | note                                    |
|-------------------------|----------|---------|-----------------------------------------|
| `CONTRACT_MAX_SIZE`     | `uint64` |         | Maximum contract size, in bytes.        |
| `MEM_MAX_ACCESS_SIZE`   | `uint64` |         | Maximum memory access size, in bytes.   |
| `VM_MAX_RAM`            | `uint64` | `2**26` | 64 MiB.                                 |
| `MESSAGE_MAX_DATA_SIZE` | `uint16` |         | Maximum size of message data, in bytes. |

## Semantics

FuelVM instructions are exactly 32 bits (4 bytes) wide and comprise of a combination of:

- Opcode: 8 bits
- Register/special register (see below) identifier: 6 bits
- Immediate value: 12, 18, or 24 bits, depending on operation

Of the 64 registers (6-bit register address space), the first `16` are reserved:

| value  | register | name                | description                                                                      |
|--------|----------|---------------------|----------------------------------------------------------------------------------|
| `0x00` | `$zero`  | zero                | Contains zero (`0`), for convenience.                                            |
| `0x01` | `$one`   | one                 | Contains one (`1`), for convenience.                                             |
| `0x02` | `$of`    | overflow            | Contains overflow/underflow of addition, subtraction, and multiplication.        |
| `0x03` | `$pc`    | program counter     | The program counter. Memory address of the current instruction.                  |
| `0x04` | `$ssp`   | stack start pointer | Memory address of bottom of current writable stack area.                         |
| `0x05` | `$sp`    | stack pointer       | Memory address on top of current writable stack area (points to free memory).    |
| `0x06` | `$fp`    | frame pointer       | Memory address of beginning of current call frame.                               |
| `0x07` | `$hp`    | heap pointer        | Memory address below the current bottom of the heap (points to used/oob memory). |
| `0x08` | `$err`   | error               | Error codes for particular operations.                                           |
| `0x09` | `$ggas`  | global gas          | Remaining gas globally.                                                          |
| `0x0A` | `$cgas`  | context gas         | Remaining gas in the context.                                                    |
| `0x0B` | `$bal`   | balance             | Received balance for this context.                                               |
| `0x0C` | `$is`    | instrs start        | Pointer to the start of the currently-executing code.                            |
| `0x0D` | `$ret`   | return value        | Return value or pointer.                                                         |
| `0x0E` | `$retl`  | return length       | Return value length in bytes.                                                    |
| `0x0F` | `$flag`  | flags               | Flags register.                                                                  |

Integers are represented in [big-endian](https://en.wikipedia.org/wiki/Endianness) format, and all operations are unsigned. Boolean `false` is `0` and Boolean `true` is `1`.

Registers are 64 bits (8 bytes) wide. Words are the same width as registers.

Persistent state (i.e. storage) is a key-value store with 32-byte keys and 32-byte values. Each contract has its own persistent state that is independent of other contracts. This is committed to in a Sparse Binary Merkle Tree.

## Flags

| value  | name           | description                                                               |
|--------|----------------|---------------------------------------------------------------------------|
| `0x01` | `F_UNSAFEMATH` | If set, undefined arithmetic zeroes target and sets `$err` without panic. |
| `0x02` | `F_WRAPPING`   | If set, overflowing arithmetic wraps around and sets `$of` without panic. |

All other flags are reserved, any must be set to zero.

## Instruction Set

A complete instruction set of the Fuel VM is documented in [the following page](./instruction-set.md).

## VM Initialization

Every time the VM runs, a single monolithic memory of size `VM_MAX_RAM` bytes is allocated, indexed by individual byte. A stack and heap memory model is used, allowing for dynamic memory allocation in higher-level languages. The stack begins at `0` and grows upward. The heap begins at `VM_MAX_RAM` and grows downward.

To initialize the VM, the following is pushed on the stack sequentially:

1. Transaction hash (`byte[32]`, word-aligned), computed as defined [here](../identifiers/transaction-id.md).
1. [`MAX_INPUTS`](../tx-format/constants.md) pairs of `(asset_id: byte[32], balance: uint64)`, of:
    1. For [predicate estimation](#predicate-estimation) and [predicate verification](#predicate-verification), zeroes.
    1. For [script execution](#script-execution), the free balance for each asset ID seen in the transaction's inputs, ordered in ascending order. If there are fewer than `MAX_INPUTS` asset IDs, the pair has a value of zero.
1. Transaction length, in bytes (`uint64`, word-aligned).
1. The [transaction, serialized](../tx-format/index.md).

Then the following registers are initialized (without explicit initialization, all registers are initialized to zero):

1. `$ssp = 32 + MAX_INPUTS*(32+8) + size(tx))`: the writable stack area starts immediately after the serialized transaction in memory (see above).
1. `$sp = $ssp`: writable stack area is empty to start.
1. `$hp = VM_MAX_RAM`: the heap area begins at the top and is empty to start.

## Contexts

There are 4 _contexts_ in the FuelVM: [predicate estimation](#predicate-estimation), [predicate verification](#predicate-verification), [scripts](#script-execution), and [calls](./instruction-set.md#call-call-contract). A context is an isolated execution environment with defined [memory ownership](#ownership) and can be _external_ or _internal_:

- External: predicate and script. `$fp` will be zero.
- Internal: call. `$fp` will be non-zero.

[Returning](./instruction-set.md#return-return-from-call) from a context behaves differently depending on whether the context is external or internal.

## Predicate Estimation

For any input of type [`InputType.Coin`](../tx-format/index.md) or [`InputType.Message`](../tx-format/index.md), a non-zero `predicateLength` field means the UTXO being spent is a [P2SH](https://en.bitcoin.it/wiki/P2SH) rather than a [P2PKH](https://en.bitcoin.it/P2PKH) output.

For each such input in the transaction, the VM is [initialized](#vm-initialization), then:

1. `$pc` and `$is` are set to the start of the input's `predicate` field.
1. `$ggas` and `$cgas` are set to the minimum of `tx.gasLimit` or `MAX_GAS_PER_PREDICATE`.

Predicate estimation will fail if gas is exhausted during execution.

During predicate mode, hitting any of the following instructions causes predicate estimation to halt, returning Boolean `false`:

1. Any [contract instruction](./instruction-set.md#contract-instructions).

In addition, during predicate mode if `$pc` is set to a value greater than the end of predicate bytecode (this would allow bytecode outside the actual predicate), predicate estimation halts returning Boolean `false`.

A predicate that halts without returning Boolean `true` would not pass verification, making the entire transaction invalid. Note that predicate validity is monotonic with respect to time (i.e. if a predicate evaluates to `true` then it will always evaluate to `true` in the future).

After successful execution, `predicateGasUsed` is set to `tx.gasLimit - $ggas`.

## Predicate Verification

For any input of type [`InputType.Coin`](../tx-format/input.md#inputcoin) or [`InputType.Message`](../tx-format/input.md#inputmessage), a non-zero `predicateLength` field means the UTXO being spent is a [P2SH](https://en.bitcoin.it/P2SH) rather than a [P2PKH](https://en.bitcoin.it/P2PKH) output.

For each such input in the transaction, the VM is [initialized](#vm-initialization), then:

1. `$pc` and `$is` are set to the start of the input's `predicate` field.
1. `$ggas` and `$cgas` are set to `predicateGasUsed`.

Predicate verification will fail if gas is exhausted during execution.

During predicate mode, hitting any [contract instruction](./instruction-set.md#contract-instructions) causes predicate verification to halt, returning Boolean `false`.

In addition, during predicate mode if `$pc` is set to a value greater than the end of predicate bytecode (this would allow bytecode outside the actual predicate), predicate verification halts returning Boolean `false`.

A predicate that halts without returning Boolean `true` does not pass verification, making the entire transaction invalid. Note that predicate validity is monotonic with respect to time (i.e. if a predicate evaluates to `true` then it will always evaluate to `true` in the future).

After execution, if `$ggas` is non-zero, predicate verification fails.

## Script Execution

If script bytecode is present, transaction validation requires execution.

The VM is [initialized](#vm-initialization), then:

1. `$pc` and `$is` are set to the start of the transaction's script bytecode.
1. `$ggas` and `$cgas` are set to `tx.gasLimit` minus the sum of `predicateGasUsed` for all predicates.

Following initialization, execution begins.

For each instruction, its gas cost `gc` is first computed. If `gc > $cgas`, deduct `$cgas` from `$ggas` and `$cgas` (i.e. spend all of `$cgas` and no more), then [revert](./instruction-set.md#rvrt-revert) immediately without actually executing the instruction. Otherwise, deduct `gc` from `$ggas` and `$cgas`.

## Call Frames

Cross-contract calls push a _call frame_ onto the stack, similar to a stack frame used in regular languages for function calls (which may be used by a high-level language that targets the FuelVM). The distinction is as follows:

1. Stack frames: store metadata across trusted internal (i.e. intra-contract) function calls. Not supported natively by the FuelVM, but may be used as an abstraction at a higher layer.
1. Call frames: store metadata across untrusted external (i.e. inter-contract) calls. Supported natively by the FuelVM.

Call frames are needed to ensure that the called contract cannot mutate the running state of the current executing contract. They segment access rights for memory: the currently-executing contracts may only write to their own call frame and their own heap.

A call frame consists of the following, word-aligned:

| bytes | type          | value      | description                                                                   |
|-------|---------------|------------|-------------------------------------------------------------------------------|
|       |               |            | **Unwritable area begins.**                                                   |
| 32    | `byte[32]`    | `to`       | Contract ID for this call.                                                    |
| 32    | `byte[32]`    | `asset_id` | asset ID of forwarded coins.                                                  |
| 8*64  | `byte[8][64]` | `regs`     | Saved registers from previous context.                                        |
| 8     | `uint64`      | `codesize` | Code size in bytes.                                 |
| 8     | `byte[8]`     | `param1`   | First parameter.                                                              |
| 8     | `byte[8]`     | `param2`   | Second parameter.                                                             |
| 1*    | `byte[]`      | `code`     | Zero-padded to 8-byte alignment, but individual instructions are not aligned. |
|       |               |            | **Unwritable area ends.**                                                     |
| *     |               |            | Call frame's stack.                                                           |

## Ownership

Whenever memory is written to (i.e. with [`SB`](./instruction-set.md#sb-store-byte) or [`SW`](./instruction-set.md#sw-store-word)), or write access is granted (i.e. with [`CALL`](./instruction-set.md#call-call-contract)), ownership must be checked.

If the context is external, the owned memory range is:

1. `[$ssp, $sp)`: the writable stack area.
1. `[$hp, VM_MAX_RAM)`: the heap area allocated by this script or predicate.

If the context is internal, the owned memory range for a call frame is:

1. `[$ssp, $sp)`: the writable stack area of the call frame.
1. `[$hp, $fp->$hp)`: the heap area allocated by this call frame.
