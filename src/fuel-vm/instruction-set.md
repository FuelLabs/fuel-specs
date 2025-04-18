# FuelVM Instruction Set

- [Reading Guide](#reading-guide)
- [Arithmetic/Logic (ALU) Instructions](#arithmeticlogic-alu-instructions)
  - [`ADD`: Add](#add-add)
  - [`ADDI`: Add immediate](#addi-add-immediate)
  - [`AND`: AND](#and-and)
  - [`ANDI`: AND immediate](#andi-and-immediate)
  - [`DIV`: Divide](#div-divide)
  - [`DIVI`: Divide immediate](#divi-divide-immediate)
  - [`EQ`: Equals](#eq-equals)
  - [`EXP`: Exponentiate](#exp-exponentiate)
  - [`EXPI`: Exponentiate immediate](#expi-exponentiate-immediate)
  - [`GT`: Greater than](#gt-greater-than)
  - [`LT`: Less than](#lt-less-than)
  - [`MLOG`: Math logarithm](#mlog-math-logarithm)
  - [`MOD`: Modulus](#mod-modulus)
  - [`MODI`: Modulus immediate](#modi-modulus-immediate)
  - [`MOVE`: Move](#move-move)
  - [`MOVI`: Move immediate](#movi-move-immediate)
  - [`MROO`: Math root](#mroo-math-root)
  - [`MUL`: Multiply](#mul-multiply)
  - [`MULI`: Multiply immediate](#muli-multiply-immediate)
  - [`MLDV`: Fused multiply-divide](#mldv-fused-multiply-divide)
  - [`NIOP`: Narrow integer operation](#niop-narrow-integer-operation)
  - [`NOOP`: No operation](#noop-no-operation)
  - [`NOT`: Invert](#not-invert)
  - [`OR`: OR](#or-or)
  - [`ORI`: OR immediate](#ori-or-immediate)
  - [`SLL`: Shift left logical](#sll-shift-left-logical)
  - [`SLLI`: Shift left logical immediate](#slli-shift-left-logical-immediate)
  - [`SRL`: Shift right logical](#srl-shift-right-logical)
  - [`SRLI`: Shift right logical immediate](#srli-shift-right-logical-immediate)
  - [`SUB`: Subtract](#sub-subtract)
  - [`SUBI`: Subtract immediate](#subi-subtract-immediate)
  - [`WDCM`: 128-bit integer comparison](#wdcm-128-bit-integer-comparison)
  - [`WQCM`: 256-bit integer comparison](#wqcm-256-bit-integer-comparison)
  - [`WDOP`: Misc 128-bit integer operations](#wdop-misc-128-bit-integer-operations)
  - [`WQOP`: Misc 256-bit integer operations](#wqop-misc-256-bit-integer-operations)
  - [`WDML`: Multiply 128-bit integers](#wdml-multiply-128-bit-integers)
  - [`WQML`: Multiply 256-bit integers](#wqml-multiply-256-bit-integers)
  - [`WDDV`: 128-bit integer division](#wddv-128-bit-integer-division)
  - [`WQDV`: 256-bit integer division](#wqdv-256-bit-integer-division)
  - [`WDMD`: 128-bit integer fused multiply-divide](#wdmd-128-bit-integer-fused-multiply-divide)
  - [`WQMD`: 256-bit integer fused multiply-divide](#wqmd-256-bit-integer-fused-multiply-divide)
  - [`WDAM`: Modular 128-bit integer addition](#wdam-modular-128-bit-integer-addition)
  - [`WQAM`: Modular 256-bit integer addition](#wqam-modular-256-bit-integer-addition)
  - [`WDMM`: Modular 128-bit integer multiplication](#wdmm-modular-128-bit-integer-multiplication)
  - [`WQMM`: Modular 256-bit integer multiplication](#wqmm-modular-256-bit-integer-multiplication)
  - [`XOR`: XOR](#xor-xor)
  - [`XORI`: XOR immediate](#xori-xor-immediate)
- [Control Flow Instructions](#control-flow-instructions)
  - [`JMP`: Jump](#jmp-jump)
  - [`JI`: Jump immediate](#ji-jump-immediate)
  - [`JNE`: Jump if not equal](#jne-jump-if-not-equal)
  - [`JNEI`: Jump if not equal immediate](#jnei-jump-if-not-equal-immediate)
  - [`JNZI`: Jump if not zero immediate](#jnzi-jump-if-not-zero-immediate)
  - [`JMPB`: Jump relative backwards](#jmpb-jump-relative-backwards)
  - [`JMPF`: Jump relative forwards](#jmpf-jump-relative-forwards)
  - [`JNZB`: Jump if not zero relative backwards](#jnzb-jump-if-not-zero-relative-backwards)
  - [`JNZF`: Jump if not zero relative forwards](#jnzf-jump-if-not-zero-relative-forwards)
  - [`JNEB`: Jump if not equal relative backwards](#jneb-jump-if-not-equal-relative-backwards)
  - [`JNEF`: Jump if not equal relative forwards](#jnef-jump-if-not-equal-relative-forwards)
  - [`RET`: Return from context](#ret-return-from-context)
- [Memory Instructions](#memory-instructions)
  - [`ALOC`: Allocate memory](#aloc-allocate-memory)
  - [`CFE`: Extend call frame](#cfe-extend-call-frame)
  - [`CFEI`: Extend call frame immediate](#cfei-extend-call-frame-immediate)
  - [`CFS`: Shrink call frame](#cfs-shrink-call-frame)
  - [`CFSI`: Shrink call frame immediate](#cfsi-shrink-call-frame-immediate)
  - [`LB`: Load byte](#lb-load-byte)
  - [`LQW`: Load quarter word](#lqw-load-quarter-word)
  - [`LHW`: Load half word](#lhw-load-half-word)
  - [`LW`: Load word](#lw-load-word)
  - [`MCL`: Memory clear](#mcl-memory-clear)
  - [`MCLI`: Memory clear immediate](#mcli-memory-clear-immediate)
  - [`MCP`: Memory copy](#mcp-memory-copy)
  - [`MCPI`: Memory copy immediate](#mcpi-memory-copy-immediate)
  - [`MEQ`: Memory equality](#meq-memory-equality)
  - [`POPH`: Pop a set of high registers from stack](#poph-pop-a-set-of-high-registers-from-stack)
  - [`POPL`: Pop a set of low registers from stack](#popl-pop-a-set-of-low-registers-from-stack)
  - [`PSHH`: Push a set of high registers to stack](#pshh-push-a-set-of-high-registers-to-stack)
  - [`PSHL`: Push a set of low registers to stack](#pshl-push-a-set-of-low-registers-to-stack)
  - [`SB`: Store byte](#sb-store-byte)
  - [`SQW`: Store quarter word](#sqw-store-quarter-word)
  - [`SHW`: Store half word](#shw-store-half-word)
  - [`SW`: Store word](#sw-store-word)
- [Contract Instructions](#contract-instructions)
  - [`BAL`: Balance of contract ID](#bal-balance-of-contract-id)
  - [`BHEI`: Block height](#bhei-block-height)
  - [`BHSH`: Block hash](#bhsh-block-hash)
  - [`BURN`: Burn existing coins](#burn-burn-existing-coins)
  - [`CALL`: Call contract](#call-call-contract)
  - [`CB`: Coinbase contract id](#cb-coinbase-contract-id)
  - [`CCP`: Code copy](#ccp-code-copy)
  - [`CROO`: Code Merkle root](#croo-code-merkle-root)
  - [`CSIZ`: Code size](#csiz-code-size)
  - [`LDC`: Load code from an external contract, blob or memory](#ldc-load-code-from-an-external-contract-blob-or-memory)
  - [`LOG`: Log event](#log-log-event)
  - [`LOGD`: Log data event](#logd-log-data-event)
  - [`MINT`: Mint new coins](#mint-mint-new-coins)
  - [`RETD`: Return from context with data](#retd-return-from-context-with-data)
  - [`RVRT`: Revert](#rvrt-revert)
  - [`SMO`: Send message to output](#smo-send-message-out)
  - [`SCWQ`: State clear sequential 32 byte slots](#scwq-state-clear-sequential-32-byte-slots)
  - [`SRW`: State read word](#srw-state-read-word)
  - [`SRWQ`: State read sequential 32 byte slots](#srwq-state-read-sequential-32-byte-slots)
  - [`SWW`: State write word](#sww-state-write-word)
  - [`SWWQ`: State write sequential 32 byte slots](#swwq-state-write-sequential-32-byte-slots)
  - [`TIME`: Timestamp at height](#time-timestamp-at-height)
  - [`TR`: Transfer coins to contract](#tr-transfer-coins-to-contract)
  - [`TRO`: Transfer coins to output](#tro-transfer-coins-to-output)
- [Blob Instructions](#blob-instructions)
  - [`BSIZ`: Blob size](#bsiz-blob-size)
  - [`BLDD`: Load data from a blob](#bldd-load-data-from-a-blob)
- [Cryptographic Instructions](#cryptographic-instructions)
  - [`ECK1`: Secp251k1 signature recovery](#eck1-secp256k1-signature-recovery)
  - [`ECR1`: Secp256r1 signature recovery](#ecr1-secp256r1-signature-recovery)
  - [`ED19`: EdDSA curve25519 verification](#ed19-eddsa-curve25519-verification)
  - [`K256`: keccak-256](#k256-keccak-256)
  - [`S256`: SHA-2-256](#s256-sha-2-256)
  - [`ECOP`: Elliptic curve operation](#ecop-elliptic-curve-point-operation)
  - [`EPAR`: Elliptic curve point pairing check](#epar-elliptic-curve-point-pairing-check)
- [Other Instructions](#other-instructions)
  - [`ECAL`: Call external function](#ecal-call-external-function)
  - [`FLAG`: Set flags](#flag-set-flags)
  - [`GM`: Get metadata](#gm-get-metadata)
  - [`GTF`: Get transaction fields](#gtf-get-transaction-fields)

## Reading Guide

This page provides a description of all instructions for the FuelVM. Encoding is read as a sequence of one 8-bit value (the opcode identifier) followed by four 6-bit values (the register identifiers or immediate value). A single `i` indicates a 6-bit immediate value, `i i` indicates a 12-bit immediate value, `i i i` indicates an 18-bit immediate value, and `i i i i` indicates a 24-bit immediate value. All immediate values are interpreted as big-endian unsigned integers. If the instruction would be shorter than the full 32 bits, the remaining part is reserved and must be zero.

- The syntax `MEM[x, y]` used in this page means the memory range starting at byte `x`, of length `y` bytes.
- The syntax `STATE[x, y]` used in this page means the sequence of storage slots starting at key `x` and spanning `y` bytes.

### Panics

Some instructions may _panic_, i.e. enter an unrecoverable state. Additionally, attempting to execute an instruction not in this list causes a panic and consumes no gas. Instructions with reserved part having a non-zero value will likewise panic.  How a panic is handled depends on [context](./index.md#contexts):

- In a predicate context, cease VM execution and return `false`.
- In other contexts, revert (described below).

On a non-predicate panic, append a receipt to the list of receipts:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Panic`                                                       |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

then append an additional receipt to the list of receipts:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `1`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

### Receipts

The number of receipts is limited to 2<sup>16</sup>, with the last two reserved to panic and script result receipts. Trying to add any other receipts after 2<sup>16</sup>-2 will panic.

### Effects

A few instructions are annotated with the _effects_ they produce, the table below explains each effect:

| effect name        | description                                        |
|--------------------|----------------------------------------------------|
| Storage read       | Instruction reads from storage slots               |
| Storage write      | Instruction writes to storage slots                |
| External call      | External contract call instruction                 |
| Balance tree read  | Instruction reads from the balance tree            |
| Balance tree write | Instruction writes to the balance tree             |
| Output message     | Instruction sends a message to a recipient address |

If an instruction is not annotated with an effect, it means it does not produce any of the aforementioned affects.

## Arithmetic/Logic (ALU) Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

Normally, if the result of an ALU operation is mathematically undefined (e.g. dividing by zero),
the VM panics. However, if the [`F_UNSAFEMATH`](./index.md#flags) flag is set, `$err` is set to `true`
and execution continues.

If an operation would overflow, so that the result doesn't fit into the target field, the VM will panic.
Results below zero are also considered overflows. If the [`F_WRAPPING`](./index.md#flags) flag is set,
instead `$of` is set to `true` or the overflowing part of the result, depending on the operation.

### `ADD`: Add

|             |                        |
|-------------|------------------------|
| Description | Adds two registers.    |
| Operation   | ```$rA = $rB + $rC;``` |
| Syntax      | `add $rA, $rB, $rC`    |
| Encoding    | `0x00 rA rB rC -`      |
| Notes       |                        |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### `ADDI`: Add immediate

|             |                                         |
|-------------|-----------------------------------------|
| Description | Adds a register and an immediate value. |
| Operation   | ```$rA = $rB + imm;```                  |
| Syntax      | `addi $rA, $rB, immediate`              |
| Encoding    | `0x00 rA rB i i`                        |
| Notes       |                                         |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### `AND`: AND

|             |                             |
|-------------|-----------------------------|
| Description | Bitwise ANDs two registers. |
| Operation   | ```$rA = $rB & $rC;```      |
| Syntax      | `and $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`           |
| Notes       |                             |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `ANDI`: AND immediate

|             |                                                 |
|-------------|-------------------------------------------------|
| Description | Bitwise ANDs a register and an immediate value. |
| Operation   | ```$rA = $rB & imm;```                          |
| Syntax      | `andi $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                                |
| Notes       |                                                 |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`imm` is extended to 64 bits, with the high 52 bits set to `0`.

`$of` and `$err` are cleared.

### `DIV`: Divide

|             |                         |
|-------------|-------------------------|
| Description | Divides two registers.  |
| Operation   | ```$rA = $rB // $rC;``` |
| Syntax      | `div $rA, $rB, $rC`     |
| Encoding    | `0x00 rA rB rC -`       |
| Notes       |                         |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If `$rC == 0`, `$rA` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### `DIVI`: Divide immediate

|             |                                            |
|-------------|--------------------------------------------|
| Description | Divides a register and an immediate value. |
| Operation   | ```$rA = $rB // imm;```                    |
| Syntax      | `divi $rA, $rB, imm`                       |
| Encoding    | `0x00 rA rB i i`                           |
| Notes       |                                            |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If `imm == 0`, `$rA` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### `EQ`: Equals

|             |                                      |
|-------------|--------------------------------------|
| Description | Compares two registers for equality. |
| Operation   | ```$rA = $rB == $rC;```              |
| Syntax      | `eq $rA, $rB, $rC`                   |
| Encoding    | `0x00 rA rB rC -`                    |
| Notes       |                                      |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `EXP`: Exponentiate

|             |                                              |
|-------------|----------------------------------------------|
| Description | Raises one register to the power of another. |
| Operation   | ```$rA = $rB ** $rC;```                      |
| Syntax      | `exp $rA, $rB, $rC`                          |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If the result cannot fit in 8 bytes, `$of` is set to `1` and `$rA` is instead set to `0`, otherwise `$of` is cleared.

`$err` is cleared.

### `EXPI`: Exponentiate immediate

|             |                                                         |
|-------------|---------------------------------------------------------|
| Description | Raises one register to the power of an immediate value. |
| Operation   | ```$rA = $rB ** imm;```                                 |
| Syntax      | `expi $rA, $rB, imm`                                    |
| Encoding    | `0x00 rA rB i i`                                        |
| Notes       |                                                         |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If the result cannot fit in 8 bytes, `$of` is set to `1` and `$rA` is instead set to `0`, otherwise `$of` is cleared.

`$err` is cleared.

### `GT`: Greater than

|             |                                          |
|-------------|------------------------------------------|
| Description | Compares two registers for greater-than. |
| Operation   | ```$rA = $rB > $rC;```                   |
| Syntax      | `gt $rA, $rB, $rC`                       |
| Encoding    | `0x00 rA rB rC -`                        |
| Notes       |                                          |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `LT`: Less than

|             |                                       |
|-------------|---------------------------------------|
| Description | Compares two registers for less-than. |
| Operation   | ```$rA = $rB < $rC;```                |
| Syntax      | `lt $rA, $rB, $rC`                    |
| Encoding    | `0x00 rA rB rC -`                     |
| Notes       |                                       |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `MLOG`: Math logarithm

|             |                                              |
|-------------|----------------------------------------------|
| Description | The (integer) logarithm base `$rC` of `$rB`. |
| Operation   | ```$rA = math.floor(math.log($rB, $rC));```  |
| Syntax      | `mlog $rA, $rB, $rC`                         |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If `$rB == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

If `$rC <= 1`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### `MOD`: Modulus

|             |                                    |
|-------------|------------------------------------|
| Description | Modulo remainder of two registers. |
| Operation   | ```$rA = $rB % $rC;```             |
| Syntax      | `mod $rA, $rB, $rC`                |
| Encoding    | `0x00 rA rB rC -`                  |
| Notes       |                                    |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If `$rC == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### `MODI`: Modulus immediate

|             |                                                        |
|-------------|--------------------------------------------------------|
| Description | Modulo remainder of a register and an immediate value. |
| Operation   | ```$rA = $rB % imm;```                                 |
| Syntax      | `modi $rA, $rB, imm`                                   |
| Encoding    | `0x00 rA rB i i`                                       |
| Notes       |                                                        |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If `imm == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### `MOVE`: Move

|             |                                    |
|-------------|------------------------------------|
| Description | Copy from one register to another. |
| Operation   | ```$rA = $rB;```                   |
| Syntax      | `move $rA, $rB`                    |
| Encoding    | `0x00 rA rB - -`                   |
| Notes       |                                    |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `MOVI`: Move immediate

|             |                                          |
|-------------|------------------------------------------|
| Description | Copy an immediate value into a register. |
| Operation   | ```$rA = imm;```                         |
| Syntax      | `movi $rA, imm`                          |
| Encoding    | `0x00 rA i i i`                          |
| Notes       |                                          |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `MROO`: Math root

|             |                                              |
|-------------|----------------------------------------------|
| Description | The (integer) `$rCth` root of `$rB`.         |
| Operation   | ```$rA = math.floor(math.root($rB, $rC));``` |
| Syntax      | `mroo $rA, $rB, $rC`                         |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

If `$rC == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### `MUL`: Multiply

|             |                           |
|-------------|---------------------------|
| Description | Multiplies two registers. |
| Operation   | ```$rA = $rB * $rC;```    |
| Syntax      | `mul $rA, $rB, $rC`       |
| Encoding    | `0x00 rA rB rC -`         |
| Notes       |                           |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### `MULI`: Multiply immediate

|             |                                               |
|-------------|-----------------------------------------------|
| Description | Multiplies a register and an immediate value. |
| Operation   | ```$rA = $rB * imm;```                        |
| Syntax      | `mul $rA, $rB, imm`                           |
| Encoding    | `0x00 rA rB i i`                              |
| Notes       |                                               |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### `MLDV`: Fused multiply-divide

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Multiplies two registers with arbitrary precision, then divides by a third register. |
| Operation   | `a = (b * c) / d;`                                                                   |
| Syntax      | `mldv $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                   |
| Notes       | Division by zero is treated as division by `1 << 64` instead.                        |

If the divisor (`$rD`) is zero, then instead the value is divided by `1 << 64`. This returns the higher half of the 128-bit multiplication result. This operation never overflows.

If the result of after the division doesn't fit into a register, `$of` is assigned the overflow of the operation. Otherwise, `$of` is cleared.

`$err` is cleared.

### `NIOP`: Narrow integer operation

|             |                                                                                     |
|-------------|-------------------------------------------------------------------------------------|
| Description | Perform an ALU operation with overflow handing for 8, 16 or 32 bit integers         |
| Operation   | `$rA = op($rB,$rC);`                                                                |
| Syntax      | `niop $rA, $rB, $rC, imm`                                                           |
| Encoding    | `0x00 rA rB rC i`                                                                   |
| Notes       | Operations that would be identical with their 64-bit counterparts are not available |

The six-bit immediate value is used to select operating mode, as follows:

Bits     | Short name | Description
---------|------------|-------------------------------------
`..XXXX` | `op`       | Operation selection, see below
`XX....` | `width`    | Operation width, see below

Then the actual operation that's performed:

`op` | Name | Description
-----|-------|---------------------------
0    | `add` | Add (`a = b + c`)
1    | `mul` | Multiply (`a = b * c`)
2    | `exp` | Exponentiate (`a = b ** c`)
3    | `sll` | Bit shift left (logical) (`a = b << c`)
4    | `xnor`| Bitwise xnor (`a = b ^ (!c)`).
other| -     | Reserved and must not be used

And operation width:

`width`| Bits
-------|------
0      | 8
1      | 16
2      | 32
3      | Reserved and must not be used

All operations first truncate their operands to the given bit width,
then perform the operation with overflow checking on that size. The
result always fits within the bit width of the operation.

Operations set `$of` and `$err` similarly to their 64-bit counterparts.
`XNOR` has no counterpart, and it always clears both `$of` and `$err`.

Panic if:

- Reserved bits of the immediate are set
- `$rA` is a [reserved register](./index.md#semantics)

### `NOOP`: No operation

|             |                        |
|-------------|------------------------|
| Description | Performs no operation. |
| Operation   |                        |
| Syntax      | `noop`                 |
| Encoding    | `0x00 - - - -`         |
| Notes       |                        |

`$of` and `$err` are cleared.

### `NOT`: Invert

|             |                         |
|-------------|-------------------------|
| Description | Bitwise NOT a register. |
| Operation   | ```$rA = ~$rB;```       |
| Syntax      | `not $rA, $rB`          |
| Encoding    | `0x00 rA rB - -`        |
| Notes       |                         |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `OR`: OR

|             |                            |
|-------------|----------------------------|
| Description | Bitwise ORs two registers. |
| Operation   | ```$rA = $rB \| $rC;```    |
| Syntax      | `or $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`          |
| Notes       |                            |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `ORI`: OR immediate

|             |                                                |
|-------------|------------------------------------------------|
| Description | Bitwise ORs a register and an immediate value. |
| Operation   | ```$rA = $rB \| imm;```                        |
| Syntax      | `ori $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                               |
| Notes       |                                                |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`imm` is extended to 64 bits, with the high 52 bits set to `0`.

`$of` and `$err` are cleared.

### `SLL`: Shift left logical

|             |                                       |
|-------------|---------------------------------------|
| Description | Left shifts a register by a register. |
| Operation   | ```$rA = $rB << $rC;```               |
| Syntax      | `sll $rA, $rB, $rC`                   |
| Encoding    | `0x00 rA rB rC -`                     |
| Notes       | Zeroes are shifted in.                |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `SLLI`: Shift left logical immediate

|             |                                               |
|-------------|-----------------------------------------------|
| Description | Left shifts a register by an immediate value. |
| Operation   | ```$rA = $rB << imm;```                       |
| Syntax      | `slli $rA, $rB, imm`                          |
| Encoding    | `0x00 rA rB i i`                              |
| Notes       | Zeroes are shifted in.                        |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `SRL`: Shift right logical

|             |                                        |
|-------------|----------------------------------------|
| Description | Right shifts a register by a register. |
| Operation   | ```$rA = $rB >> $rC;```                |
| Syntax      | `srl $rA, $rB, $rC`                    |
| Encoding    | `0x00 rA rB rC -`                      |
| Notes       | Zeroes are shifted in.                 |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `SRLI`: Shift right logical immediate

|             |                                                |
|-------------|------------------------------------------------|
| Description | Right shifts a register by an immediate value. |
| Operation   | ```$rA = $rB >> imm;```                        |
| Syntax      | `srli $rA, $rB, imm`                           |
| Encoding    | `0x00 rA rB i i`                               |
| Notes       | Zeroes are shifted in.                         |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `SUB`: Subtract

|             |                                                  |
|-------------|--------------------------------------------------|
| Description | Subtracts two registers.                         |
| Operation   | ```$rA = $rB - $rC;```                           |
| Syntax      | `sub $rA, $rB, $rC`                              |
| Encoding    | `0x00 rA rB rC -`                                |
| Notes       |                                                  |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### `SUBI`: Subtract immediate

|             |                                                  |
|-------------|--------------------------------------------------|
| Description | Subtracts a register and an immediate value.     |
| Operation   | ```$rA = $rB - imm;```                           |
| Syntax      | `subi $rA, $rB, imm`                             |
| Encoding    | `0x00 rA rB i i`                                 |
| Notes       |                                                  |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### `WDCM`: 128-bit integer comparison

|             |                                                                               |
|-------------|-------------------------------------------------------------------------------|
| Description | Compare or examine two 128-bit integers using selected mode                   |
| Operation   | `b = mem[$rB,16];`<br>`c = indirect?mem[$rC,16]:$rC;`<br>`$rA = cmp_op(b,c);` |
| Syntax      | `wdcm $rA, $rB, $rC, imm`                                                     |
| Encoding    | `0x00 rA rB rC i`                                                             |
| Notes       |                                                                               |

The six-bit immediate value is used to select operating mode, as follows:

Bits     | Short name | Description
---------|------------|-------------------------------------
`...XXX` | `mode`     | Compare mode selection
`.XX...` | `reserved` | Reserved and must be zero
`X.....` | `indirect` | Is rhs operand (`$rC`) indirect or not

Then the actual operation that's performed:

`mode` | Name | Description
-------|------|-------------------------------------------------------------
0      | `eq`   | Equality (`==`)
1      | `ne`   | Inequality (`!=`)
2      | `lt`   | Less than (`<`)
3      | `gt`   | Greater than (`>`)
4      | `lte`  | Less than or equals (`<=`)
5      | `gte`  | Greater than or equals (`>=`)
6      | `lzc`  | Leading zero count the lhs argument (`lzcnt`). Discards rhs.
7      | -    | Reserved and must not be used

The leading zero count can be used to compute rounded-down log2 of a number using the following formula `TOTAL_BITS - 1 - lzc(n)`. Note that `log2(0)` is undefined, and will lead to integer overflow with this method.

Clears `$of` and `$err`.

Panic if:

- A reserved compare mode is given
- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + 16` overflows or `> VM_MAX_RAM`
- `indirect == 1` and `$rC + 16` overflows or `> VM_MAX_RAM`

### `WQCM`: 256-bit integer comparison

|             |                                                                               |
|-------------|-------------------------------------------------------------------------------|
| Description | Compare or examine two 256-bit integers using selected mode                   |
| Operation   | `b = mem[$rB,32];`<br>`c = indirect?mem[$rC,32]:$rC;`<br>`$rA = cmp_op(b,c);` |
| Syntax      | `wqcm $rA, $rB, $rC, imm`                                                     |
| Encoding    | `0x00 rA rB rC i`                                                             |
| Notes       |                                                                               |

The immediate value is interpreted identically to `WDCM`.

Clears `$of` and `$err`.

Panic if:

- A reserved compare mode is given
- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `indirect == 1` and `$rC + 32` overflows or `> VM_MAX_RAM`

### `WDOP`: Misc 128-bit integer operations

|             |                                                                                   |
|-------------|-----------------------------------------------------------------------------------|
| Description | Perform an ALU operation on two 128-bit integers                                  |
| Operation   | `b = mem[$rB,16];`<br>`c = indirect?mem[$rC,16]:$rC;`<br>`mem[$rA,16] = op(b,c);` |
| Syntax      | `wdop $rA, $rB, $rC, imm`                                                         |
| Encoding    | `0x00 rA rB rC i`                                                                 |
| Notes       |                                                                                   |

The six-bit immediate value is used to select operating mode, as follows:

Bits     | Short name | Description
---------|------------|-------------------------------------
`...XXX` | `op`       | Operation selection, see below
`.XX...` | `reserved` | Reserved and must be zero
`X.....` | `indirect` | Is rhs operand (`$rC`) indirect or not

Then the actual operation that's performed:

`op` | Name | Description
-----|------|---------------------------
0    | `add`  | Add
1    | `sub`  | Subtract
2    | `not`  | Invert bits (discards rhs)
3    | `or`   | Bitwise or
4    | `xor`  | Bitwise exclusive or
5    | `and`  | Bitwise and
6    | `shl`  | Shift left (logical)
7    | `shr`  | Shift right (logical)

Operations behave `$of` and `$err` similarly to their 64-bit counterparts, except that `$of` is set to `1` instead of the overflowing part.

Panic if:

- Reserved bits of the immediate are set
- The memory range `MEM[$rA, 16]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 16` overflows or `> VM_MAX_RAM`
- `indirect == 1` and `$rC + 16` overflows or `> VM_MAX_RAM`

### `WQOP`: Misc 256-bit integer operations

|             |                                                                                   |
|-------------|-----------------------------------------------------------------------------------|
| Description | Perform an ALU operation on two 256-bit integers                                  |
| Operation   | `b = mem[$rB,32];`<br>`c = indirect?mem[$rC,32]:$rC;`<br>`mem[$rA,32] = op(b,c);` |
| Syntax      | `wqop $rA, $rB, $rC, imm`                                                         |
| Encoding    | `0x00 rA rB rC i`                                                                 |
| Notes       |                                                                                   |

The immediate value is interpreted identically to `WDOP`.

Operations behave `$of` and `$err` similarly to their 64-bit counterparts.

Panic if:

- Reserved bits of the immediate are set
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `indirect == 1` and `$rC + 32` overflows or `> VM_MAX_RAM`

### `WDML`: Multiply 128-bit integers

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Perform integer multiplication operation on two 128-bit integers.                      |
| Operation   | `b=indirect0?mem[$rB,16]:$rB;`<br>`c=indirect1?mem[$rC,16]:$rC;`<br>`mem[$rA,16]=b*c;` |
| Syntax      | `wdml $rA, $rB, $rC, imm`                                                              |
| Encoding    | `0x00 rA rB rC i`                                                                      |
| Notes       |                                                                                        |

The six-bit immediate value is used to select operating mode, as follows:

Bits     | Short name  | Description
---------|-------------|-------------------------------------
`..XXXX` | `reserved`  | Reserved and must be zero
`.X....` | `indirect0` | Is lhs operand (`$rB`) indirect or not
`X.....` | `indirect1` | Is rhs operand (`$rC`) indirect or not

`$of` is set to `1` in case of overflow, and cleared otherwise.

`$err` is cleared.

Panic if:

- Reserved bits of the immediate are set
- The memory range `MEM[$rA, 16]`  does not pass [ownership check](./index.md#ownership)
- `indirect0 == 1` and `$rB + 16` overflows or `> VM_MAX_RAM`
- `indirect1 == 1` and `$rC + 16` overflows or `> VM_MAX_RAM`

### `WQML`: Multiply 256-bit integers

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Perform integer multiplication operation on two 256-bit integers.                      |
| Operation   | `b=indirect0?mem[$rB,32]:$rB;`<br>`c=indirect1?mem[$rC,32]:$rC;`<br>`mem[$rA,32]=b*c;` |
| Syntax      | `wqml $rA, $rB, $rC, imm`                                                              |
| Encoding    | `0x00 rA rB rC i`                                                                      |
| Notes       |                                                                                        |

The immediate value is interpreted identically to `WDML`.

`$of` is set to `1` in case of overflow, and cleared otherwise.

`$err` is cleared.

Panic if:

- Reserved bits of the immediate are set
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- `indirect0 == 1` and `$rB + 32` overflows or `> VM_MAX_RAM`
- `indirect1 == 1` and `$rC + 32` overflows or `> VM_MAX_RAM`

### `WDDV`: 128-bit integer division

|             |                                                                                 |
|-------------|---------------------------------------------------------------------------------|
| Description | Divide a 128-bit integer by another.                                            |
| Operation   | `b = mem[$rB,16];`<br>`c = indirect?mem[$rC,16]:$rC;`<br>`mem[$rA,16] = b / c;` |
| Syntax      | `wddv $rA, $rB, $rC, imm`                                                       |
| Encoding    | `0x00 rA rB rC i`                                                               |
| Notes       |                                                                                 |

The six-bit immediate value is used to select operating mode, as follows:

Bits     | Short name | Description
---------|------------|-------------------------------------
`.XXXXX` | `reserved` | Reserved and must be zero
`X.....` | `indirect` | Is rhs operand (`$rC`) indirect or not

`$of` is cleared.

If the rhs operand is zero, `MEM[$rA, 16]` is cleared and `$err` is set to `true`. Otherwise, `$err` is cleared.

Panic if:

- Reserved bits of the immediate are set
- The memory range `MEM[$rA, 16]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 16` overflows or `> VM_MAX_RAM`
- `indirect == 1` and `$rC + 16` overflows or `> VM_MAX_RAM`

### `WQDV`: 256-bit integer division

|             |                                                                                 |
|-------------|---------------------------------------------------------------------------------|
| Description | Divide a 256-bit integer by another.                                            |
| Operation   | `b = mem[$rB,32];`<br>`c = indirect?mem[$rC,32]:$rC;`<br>`mem[$rA,32] = b / c;` |
| Syntax      | `wqdv $rA, $rB, $rC, imm`                                                       |
| Encoding    | `0x00 rA rB rC i`                                                               |
| Notes       |                                                                                 |

The immediate value is interpreted identically to `WDDV`.

`$of` is cleared.

If the rhs operand is zero, `MEM[$rA, 32]` is cleared and `$err` is set to `true`. Otherwise, `$err` is cleared.

Panic if:

- Reserved bits of the immediate are set
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `indirect == 1` and `$rC + 32` overflows or `> VM_MAX_RAM`

### `WDMD`: 128-bit integer fused multiply-divide

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Combined multiply-divide of 128-bit integers with arbitrary precision.                 |
| Operation   | `b=mem[$rB,16];`<br>`c=mem[$rC,16];`<br>`d=mem[$rD,16];`<br>`mem[$rA,16]=(b * c) / d;` |
| Syntax      | `wdmd $rA, $rB, $rC, $rD`                                                              |
| Encoding    | `0x00 rA rB rC rD`                                                                     |
| Notes       | Division by zero is treated as division by `1 << 128` instead.                         |

If the divisor `MEM[$rA, 16]` is zero, then instead the value is divided by `1 << 128`. This returns the higher half of the 256-bit multiplication result.

If the result of after the division is larger than operand size, `$of` is set to one. Otherwise, `$of` is cleared.

`$err` is cleared.

Panic if:

- The memory range `MEM[$rA, 16]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 16` overflows or `> VM_MAX_RAM`
- `$rC + 16` overflows or `> VM_MAX_RAM`
- `$rD + 16` overflows or `> VM_MAX_RAM`

### `WQMD`: 256-bit integer fused multiply-divide

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Combined multiply-divide of 256-bit integers with arbitrary precision.                 |
| Operation   | `b=mem[$rB,32];`<br>`c=mem[$rC,32];`<br>`d=mem[$rD,32];`<br>`mem[$rA,32]=(b * c) / d;` |
| Syntax      | `wqmd $rA, $rB, $rC, $rD`                                                              |
| Encoding    | `0x00 rA rB rC rD`                                                                     |
| Notes       | Division by zero is treated as division by `1 << 256` instead.                         |

If the divisor `MEM[$rA, 32]` is zero, then instead the value is divided by `1 << 256`. This returns the higher half of the 512-bit multiplication result.

If the result of after the division is larger than operand size, `$of` is set to one. Otherwise, `$of` is cleared.

`$err` is cleared.

Panic if:

- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- `$rD + 32` overflows or `> VM_MAX_RAM`

### `WDAM`: Modular 128-bit integer addition

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Add two 128-bit integers and compute modulo remainder with arbitrary precision.      |
| Operation   | `b=mem[$rB,16];`<br>`c=mem[$rC,16];`<br>`d=mem[$rD,16];`<br>`mem[$rA,16] = (b+c)%d;` |
| Syntax      | `wdam $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                   |
| Notes       |                                                                                      |

`$of` is cleared.

If the rhs operand is zero, `MEM[$rA, 16]` is cleared and `$err` is set to `true`. Otherwise, `$err` is cleared.

Panic if:

- The memory range `MEM[$rA, 16]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 16` overflows or `> VM_MAX_RAM`
- `$rC + 16` overflows or `> VM_MAX_RAM`
- `$rD + 16` overflows or `> VM_MAX_RAM`

### `WQAM`: Modular 256-bit integer addition

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Add two 256-bit integers and compute modulo remainder with arbitrary precision.      |
| Operation   | `b=mem[$rB,32];`<br>`c=mem[$rC,32];`<br>`d=mem[$rD,32];`<br>`mem[$rA,32] = (b+c)%d;` |
| Syntax      | `wqam $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                   |
| Notes       |                                                                                      |

`$of` is cleared.

If the rhs operand is zero, `MEM[$rA, 32]` is cleared and `$err` is set to `true`. Otherwise, `$err` is cleared.

Panic if:

- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- `$rD + 32` overflows or `> VM_MAX_RAM`

### `WDMM`: Modular 128-bit integer multiplication

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Multiply two 128-bit integers and compute modulo remainder with arbitrary precision. |
| Operation   | `b=mem[$rB,16];`<br>`c=mem[$rC,16];`<br>`d=mem[$rD,16];`<br>`mem[$rA,16] = (b*c)%d;` |
| Syntax      | `wdmm $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                   |
| Notes       |                                                                                      |

`$of` is cleared.

If the rhs operand is zero, `MEM[$rA, 16]` is cleared and `$err` is set to `true`. Otherwise, `$err` is cleared.

Panic if:

- The memory range `MEM[$rA, 16]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 16` overflows or `> VM_MAX_RAM`
- `$rC + 16` overflows or `> VM_MAX_RAM`
- `$rD + 16` overflows or `> VM_MAX_RAM`

### `WQMM`: Modular 256-bit integer multiplication

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Multiply two 256-bit integers and compute modulo remainder with arbitrary precision. |
| Operation   | `b=mem[$rB,32];`<br>`c=mem[$rC,32];`<br>`d=mem[$rD,32];`<br>`mem[$rA,32] = (b*c)%d;` |
| Syntax      | `wqmm $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                   |
| Notes       |                                                                                      |

`$of` is cleared.

If the rhs operand is zero, `MEM[$rA, 32]` is cleared and `$err` is set to `true`. Otherwise, `$err` is cleared.

Panic if:

- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- `$rD + 32` overflows or `> VM_MAX_RAM`

### `XOR`: XOR

|             |                             |
|-------------|-----------------------------|
| Description | Bitwise XORs two registers. |
| Operation   | ```$rA = $rB ^ $rC;```      |
| Syntax      | `xor $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`           |
| Notes       |                             |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

### `XORI`: XOR immediate

|             |                                                 |
|-------------|-------------------------------------------------|
| Description | Bitwise XORs a register and an immediate value. |
| Operation   | ```$rA = $rB ^ imm;```                          |
| Syntax      | `xori $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                                |
| Notes       |                                                 |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

`$of` and `$err` are cleared.

## Control Flow Instructions

### `JMP`: Jump

|             |                                                     |
|-------------|-----------------------------------------------------|
| Description | Jumps to the code instruction offset by a register. |
| Operation   | ```$pc = $is + $rA * 4;```                          |
| Syntax      | `jmp $rA`                                           |
| Encoding    | `0x00 rA - - -`                                     |
| Notes       |                                                     |

Panic if:

- `$is + $rA * 4 > VM_MAX_RAM - 1`

### `JI`: Jump immediate

|             |                                                |
|-------------|------------------------------------------------|
| Description | Jumps to the code instruction offset by `imm`. |
| Operation   | ```$pc = $is + imm * 4;```                     |
| Syntax      | `ji imm`                                       |
| Encoding    | `0x00 i i i i`                                 |
| Notes       |                                                |

Panic if:

- `$is + imm * 4 > VM_MAX_RAM - 1`

### `JNE`: Jump if not equal

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Jump to the code instruction offset by a register if `$rA` is not equal to `$rB`.    |
| Operation   | ```if $rA != $rB:```<br>```$pc = $is + $rC * 4;```<br>```else:```<br>```$pc += 4;``` |
| Syntax      | `jne $rA $rB $rC`                                                                    |
| Encoding    | `0x00 rA rB rC -`                                                                    |
| Notes       |                                                                                      |

Panic if:

- `$is + $rC * 4 > VM_MAX_RAM - 1` and the jump would be performed (i.e. `$rA != $rB`)

### `JNEI`: Jump if not equal immediate

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Jump to the code instruction offset by `imm` if `$rA` is not equal to `$rB`.         |
| Operation   | ```if $rA != $rB:```<br>```$pc = $is + imm * 4;```<br>```else:```<br>```$pc += 4;``` |
| Syntax      | `jnei $rA $rB imm`                                                                   |
| Encoding    | `0x00 rA rB i i`                                                                     |
| Notes       |                                                                                      |

Panic if:

- `$is + imm * 4 > VM_MAX_RAM - 1` and the jump would be performed (i.e. `$rA != $rB`)

### `JNZI`: Jump if not zero immediate

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Jump to the code instruction offset by `imm` if `$rA` is not equal to `$zero`.         |
| Operation   | ```if $rA != $zero:```<br>```$pc = $is + imm * 4;```<br>```else:```<br>```$pc += 4;``` |
| Syntax      | `jnzi $rA imm`                                                                         |
| Encoding    | `0x00 rA i i i`                                                                        |
| Notes       |                                                                                        |

Panic if:

- `$is + imm * 4 > VM_MAX_RAM - 1`and the jump would be performed (i.e. `$rA != $zero`)

### `JMPB`: Jump relative backwards

|             |                                          |
|-------------|------------------------------------------|
| Description | Jump `$rA + imm` instructions backwards. |
| Operation   | ```$pc -= ($rA + imm + 1) * 4;```        |
| Syntax      | `jmpb $rA imm`                           |
| Encoding    | `0x00 rA i i i`                          |
| Notes       |                                          |

Panic if:

- `$pc - ($rA + imm + 1) * 4 < 0`

### `JMPF`: Jump relative forwards

|             |                                        |
|-------------|----------------------------------------|
| Description | Jump `$rA + imm` instructions forwards |
| Operation   | ```$pc += ($rA + imm + 1) * 4;```      |
| Syntax      | `jmpf $rA imm`                         |
| Encoding    | `0x00 rA i i i`                        |
| Notes       |                                        |

Panic if:

- `$pc + ($rA + imm + 1) * 4 > VM_MAX_RAM - 1`

### `JNZB`: Jump if not zero relative backwards

|             |                                                                               |
|-------------|-------------------------------------------------------------------------------|
| Description | Jump `$rB + imm` instructions backwards if `$rA != $zero`.                    |
| Operation   | `if $rA != $zero:`<br>`$pc -= ($rB + imm + 1) * 4;`<br>`else:`<br>`$pc += 4;` |
| Syntax      | `jnzb $rA $rB imm`                                                            |
| Encoding    | `0x00 rA rB i i`                                                              |
| Notes       |                                                                               |

Panic if:

- `$pc - ($rB + imm + 1) * 4 < 0`

### `JNZF`: Jump if not zero relative forwards

|             |                                                                               |
|-------------|-------------------------------------------------------------------------------|
| Description | Jump `$rB + imm` instructions forwards if `$rA != $zero`.                     |
| Operation   | `if $rA != $zero:`<br>`$pc += ($rB + imm + 1) * 4;`<br>`else:`<br>`$pc += 4;` |
| Syntax      | `jnzf $rA $rB imm`                                                            |
| Encoding    | `0x00 rA rB i i`                                                              |
| Notes       |                                                                               |

Panic if:

- `$pc + ($rB + imm + 1) * 4 > VM_MAX_RAM - 1`

### `JNEB`: Jump if not equal relative backwards

|             |                                                                             |
|-------------|-----------------------------------------------------------------------------|
| Description | Jump `$rC + imm` instructions backwards if `$rA != $rB`.                    |
| Operation   | `if $rA != $rB:`<br>`$pc -= ($rC + imm + 1) * 4;`<br>`else:`<br>`$pc += 4;` |
| Syntax      | `jneb $rA $rB $rC imm`                                                      |
| Encoding    | `0x00 rA rB rC i`                                                           |
| Notes       |                                                                             |

Panic if:

- `$pc - ($rC + imm + 1) * 4 < 0`

### `JNEF`: Jump if not equal relative forwards

|             |                                                                             |
|-------------|-----------------------------------------------------------------------------|
| Description | Jump `$rC + imm` instructions forwards if `$rA != $rB`.                     |
| Operation   | `if $rA != $rB:`<br>`$pc += ($rC + imm + 1) * 4;`<br>`else:`<br>`$pc += 4;` |
| Syntax      | `jnef $rA $rB $rC imm`                                                      |
| Encoding    | `0x00 rA rB rC i`                                                           |
| Notes       |                                                                             |

Panic if:

- `$pc + ($rC + imm + 1) * 4 > VM_MAX_RAM - 1`

### `RET`: Return from context

|             |                                                               |
|-------------|---------------------------------------------------------------|
| Description | Returns from [context](./index.md#contexts) with value `$rA`. |
| Operation   | ```return($rA);```                                            |
| Syntax      | `ret $rA`                                                     |
| Encoding    | `0x00 rA - - -`                                               |
| Notes       | `$ret` is set to the return value from `$rA`.                 |

Append a receipt to the list of receipts:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Return`                                                      |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val`  | `uint64`      | Value of register `$rA`.                                                  |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

If current context is external, append an additional receipt to the list of receipts:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `0`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

If current context is external, cease VM execution and return `$rA`.

Returns from contract call, popping the call frame. Before popping perform the following operations.

Return the unused forwarded gas to the caller:

1. `$cgas = $cgas + $fp->$cgas` (add remaining context gas from previous context to current remaining context gas)

Set the return value:

1. `$ret = $rA`
1. `$retl = 0`

Then pop the call frame and restore all registers _except_ `$ggas`, `$cgas`, `$ret`, `$retl` and `$hp`. Afterwards, set the following registers:

1. `$pc = $pc + 4` (advance program counter from where we called)

## Memory Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### `ALOC`: Allocate memory

|             |                                           |
|-------------|-------------------------------------------|
| Description | Allocate a number of bytes from the heap. |
| Operation   | ```$hp = $hp - $rA;```                    |
| Syntax      | `aloc $rA`                                |
| Encoding    | `0x00 rA - - -`                           |
| Notes       | Newly allocated memory is zeroed.         |

Panic if:

- `$hp - $rA` underflows
- `$hp - $rA < $sp`

### `CFE`: Extend call frame

|             |                                        |
|-------------|----------------------------------------|
| Description | Extend the current call frame's stack. |
| Operation   | ```$sp = $sp + $rA```                  |
| Syntax      | `cfe $rA`                             |
| Encoding    | `0x00 rA - - -`                        |
| Notes       | Does not initialize memory.            |

Panic if:

- `$sp + $rA` overflows
- `$sp + $rA > $hp`

### `CFEI`: Extend call frame immediate

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | Extend the current call frame's stack by an immediate value. |
| Operation   | ```$sp = $sp + imm```                                        |
| Syntax      | `cfei imm`                                                   |
| Encoding    | `0x00 i i i i`                                               |
| Notes       | Does not initialize memory.                                  |

Panic if:

- `$sp + imm` overflows
- `$sp + imm > $hp`

### `CFS`: Shrink call frame

|             |                                        |
|-------------|----------------------------------------|
| Description | Shrink the current call frame's stack. |
| Operation   | ```$sp = $sp - $rA```                  |
| Syntax      | `cfs $rA`                              |
| Encoding    | `0x00 $rA - - -`                       |
| Notes       | Does not clear memory.                 |

Panic if:

- `$sp - $rA` underflows
- `$sp - $rA < $ssp`

### `CFSI`: Shrink call frame immediate

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | Shrink the current call frame's stack by an immediate value. |
| Operation   | ```$sp = $sp - imm```                                        |
| Syntax      | `cfsi imm`                                                   |
| Encoding    | `0x00 i i i i`                                               |
| Notes       | Does not clear memory.                                       |

Panic if:

- `$sp - imm` underflows
- `$sp - imm < $ssp`

### `LB`: Load byte

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | A byte is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + imm, 1];```                               |
| Syntax      | `lb $rA, $rB, imm`                                           |
| Encoding    | `0x00 rA rB i i`                                             |
| Notes       |                                                              |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + imm + 1` overflows or `> VM_MAX_RAM`

### `LQW`: Load quarter word

|             |                                                                      |
|-------------|----------------------------------------------------------------------|
| Description | A quarter word is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + (imm * 2), 2];```                                 |
| Syntax      | `lqw $rA, $rB, imm`                                                  |
| Encoding    | `0x00 rA rB i i`                                                     |
| Notes       |                                                                      |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + (imm * 2) + 2` overflows or `> VM_MAX_RAM`

### `LHW`: Load half word

|             |                                                                      |
|-------------|----------------------------------------------------------------------|
| Description | A half word is loaded from the specified address offset by `imm`.    |
| Operation   | ```$rA = MEM[$rB + (imm * 4), 4];```                                 |
| Syntax      | `lhw $rA, $rB, imm`                                                  |
| Encoding    | `0x00 rA rB i i`                                                     |
| Notes       |                                                                      |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + (imm * 4) + 4` overflows or `> VM_MAX_RAM`

### `LW`: Load word

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | A word is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + (imm * 8), 8];```                         |
| Syntax      | `lw $rA, $rB, imm`                                           |
| Encoding    | `0x00 rA rB i i`                                             |
| Notes       |                                                              |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + (imm * 8) + 8` overflows or `> VM_MAX_RAM`

### `MCL`: Memory clear

|             |                          |
|-------------|--------------------------|
| Description | Clear bytes in memory.   |
| Operation   | ```MEM[$rA, $rB] = 0;``` |
| Syntax      | `mcl $rA, $rB`           |
| Encoding    | `0x00 rA rB - -`         |
| Notes       |                          |

Panic if:

- `$rA + $rB` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, $rB]`  does not pass [ownership check](./index.md#ownership)

### `MCLI`: Memory clear immediate

|             |                          |
|-------------|--------------------------|
| Description | Clear bytes in memory.   |
| Operation   | ```MEM[$rA, imm] = 0;``` |
| Syntax      | `mcli $rA, imm`          |
| Encoding    | `0x00 rA i i i`          |
| Notes       |                          |

Panic if:

- `$rA + imm` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, imm]`  does not pass [ownership check](./index.md#ownership)

### `MCP`: Memory copy

|             |                                      |
|-------------|--------------------------------------|
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rA, $rC] = MEM[$rB, $rC];``` |
| Syntax      | `mcp $rA, $rB, $rC`                  |
| Encoding    | `0x00 rA rB rC -`                    |
| Notes       |                                      |

Panic if:

- `$rA + $rC` overflows or `> VM_MAX_RAM`
- `$rB + $rC` overflows or `> VM_MAX_RAM`
- The memory ranges `MEM[$rA, $rC]` and `MEM[$rB, $rC]` overlap
- The memory range `MEM[$rA, $rC]`  does not pass [ownership check](./index.md#ownership)

### `MCPI`: Memory copy immediate

|             |                                      |
|-------------|--------------------------------------|
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rA, imm] = MEM[$rB, imm];``` |
| Syntax      | `mcpi $rA, $rB, imm`                 |
| Encoding    | `0x00 rA rB imm imm`                 |
| Notes       |                                      |

Panic if:

- `$rA + imm` overflows or `> VM_MAX_RAM`
- `$rB + imm` overflows or `> VM_MAX_RAM`
- The memory ranges `MEM[$rA, imm]` and `MEM[$rB, imm]` overlap
- The memory range `MEM[$rA, imm]`  does not pass [ownership check](./index.md#ownership)

### `MEQ`: Memory equality

|             |                                             |
|-------------|---------------------------------------------|
| Description | Compare bytes in memory.                    |
| Operation   | ```$rA = MEM[$rB, $rD] == MEM[$rC, $rD];``` |
| Syntax      | `meq $rA, $rB, $rC, $rD`                    |
| Encoding    | `0x00 rA rB rC rD`                          |
| Notes       |                                             |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + $rD` overflows or `> VM_MAX_RAM`
- `$rC + $rD` overflows or `> VM_MAX_RAM`

### `PSHH`: Push a set of high registers to stack

|             |                                                                                   |
|-------------|-----------------------------------------------------------------------------------|
| Description | Push a set of registers from range 40..64 to the stack in order.                  |
| Operation   | `tmp=$sp;`<br>`$sp+=popcnt(imm)*8;`<br>`MEM[tmp,$sp]=registers[40..64].mask(imm)` |
| Syntax      | `pshh imm`                                                                        |
| Encoding    | `0x00 i i i i`                                                                    |
| Notes       | The immediate value is used as a bitmask for selecting the registers.             |

The nth bit of the bitmask corresponds to nth entry of the register range. In other words, the most significant (i.e. leftmost) bit of the bitmask corresponds to the highest register index. So for instance bitmask `011000000000000000000000` pushes the register 61 followed by register 62.

Panic if:

- `$sp + popcnt(imm)*8` overflows
- `$sp + popcnt(imm)*8 > $hp`

### `PSHL`: Push a set of low registers to stack

|             |                                                                                   |
|-------------|-----------------------------------------------------------------------------------|
| Description | Push a set of registers from range 16..40 to the stack in order.                  |
| Operation   | `tmp=$sp;`<br>`$sp+=popcnt(imm)*8;`<br>`MEM[tmp,$sp]=registers[16..40].mask(imm)` |
| Syntax      | `pshl imm`                                                                        |
| Encoding    | `0x00 i i i i`                                                                    |
| Notes       | The immediate value is used as a bitmask for selecting the registers.             |

The nth bit of the bitmask corresponds to nth entry of the register range. In other words, the most significant (i.e. leftmost) bit of the bitmask corresponds to the highest register index. So for instance bitmask `011000000000000000000000` pushes the register 37 followed by register 38.

Panic if:

- `$sp + popcnt(imm)*8` overflows
- `$sp + popcnt(imm)*8 > $hp`

### `POPH`: Pop a set of high registers from stack

|             |                                                                                       |
|-------------|---------------------------------------------------------------------------------------|
| Description | Pop to a set of registers from range 40..64 from the stack.                           |
| Operation   | `tmp=$sp-popcnt(imm)*8;`<br>`registers[40..64].mask(imm)=MEM[tmp,$sp]`<br>`$sp-=tmp;` |
| Syntax      | `poph imm`                                                                            |
| Encoding    | `0x00 i i i i`                                                                        |
| Notes       | The immediate value is used as a bitmask for selecting the registers.                 |

The nth bit of the bitmask corresponds to nth entry of the register range. In other words, the most significant (i.e. leftmost) bit of the bitmask corresponds to the highest register index. So for instance bitmask `011000000000000000000000` pops the register 62 followed by register 61.

Note that the order is reverse from `PSHH`, so that `PSHH a; POPH a` returns to the original state.

Panic if:

- `$sp - popcnt(imm)*8` overflows
- `$sp - popcnt(imm)*8 < $ssp`

### `POPL`: Pop a set of low registers from stack

|             |                                                                                       |
|-------------|---------------------------------------------------------------------------------------|
| Description | Pop to a set of registers from range 16..40 from the stack.                           |
| Operation   | `tmp=$sp-popcnt(imm)*8;`<br>`registers[16..40].mask(imm)=MEM[tmp,$sp]`<br>`$sp-=tmp;` |
| Syntax      | `poph imm`                                                                            |
| Encoding    | `0x00 i i i i`                                                                        |
| Notes       | The immediate value is used as a bitmask for selecting the registers.                 |

The nth bit of the bitmask corresponds to nth entry of the register range. In other words, the most significant (i.e. leftmost) bit of the bitmask corresponds to the highest register index. So for instance bitmask `011000000000000000000000` pops the register 38 followed by register 37.

Note that the order is reverse from `PSHL`, so that `PSHL a; POPL a` returns to the original state.

Panic if:

- `$sp - popcnt(imm)*8` overflows
- `$sp - popcnt(imm)*8 < $ssp`

### `SB`: Store byte

|             |                                                                                     |
|-------------|-------------------------------------------------------------------------------------|
| Description | The least significant byte of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + imm, 1] = $rB[7, 1];```                                                |
| Syntax      | `sb $rA, $rB, imm`                                                                  |
| Encoding    | `0x00 rA rB i i`                                                                    |
| Notes       |                                                                                     |

Panic if:

- `$rA + imm + 1` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA + imm, 1]`  does not pass [ownership check](./index.md#ownership)

### `SQW`: Store quarter word

|             |                                                                                               |
|-------------|-----------------------------------------------------------------------------------------------|
| Description | The two least significant bytes of  of `$rB` is stored at the address `$rA` offset by `imm`.  |
| Operation   | ```MEM[$rA + (imm * 2), 2] = $rB;```                                                          |
| Syntax      | `sqw $rA, $rB, imm`                                                                           |
| Encoding    | `0x00 rA rB i i`                                                                              |
| Notes       |                                                                                               |

### `SHW`: Store half word

|             |                                                                                               |
|-------------|-----------------------------------------------------------------------------------------------|
| Description | The four least significant bytes of  of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + (imm * 4), 4] = $rB;```                                                          |
| Syntax      | `shw $rA, $rB, imm`                                                                           |
| Encoding    | `0x00 rA rB i i`                                                                              |
| Notes       |                                                                                               |

Panic if:

- `$rA + (imm * 4) + 4` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA + (imm * 4), 4]`  does not pass [ownership check](./index.md#ownership)

### `SW`: Store word

|             |                                                                    |
|-------------|--------------------------------------------------------------------|
| Description | The value of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + (imm * 8), 8] = $rB;```                               |
| Syntax      | `sw $rA, $rB, imm`                                                 |
| Encoding    | `0x00 rA rB i i`                                                   |
| Notes       |                                                                    |

Panic if:

- `$rA + (imm * 8) + 8` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA + (imm * 8), 8]`  does not pass [ownership check](./index.md#ownership)

## Contract Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation, except for [CALL](#call-call-contract), [`RETD`](#retd-return-from-context-with-data) and [`RVRT`](#rvrt-revert).

### `BAL`: Balance of contract ID

|             |                                                                              |
|-------------|------------------------------------------------------------------------------|
| Description | Set `$rA` to the balance of asset ID at `$rB` for contract with ID at `$rC`. |
| Operation   | ```$rA = balance(MEM[$rB, 32], MEM[$rC, 32]);```                             |
| Syntax      | `bal $rA, $rB, $rC`                                                          |
| Encoding    | `0x00 rA rB rC -`                                                            |
| Effects     | Balance tree read                                                            |
| Notes       |                                                                              |

Where helper `balance(asset_id: byte[32], contract_id: byte[32]) -> uint64` returns the current balance of `asset_id` of contract with ID `contract_id`.

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- Contract with ID `MEM[$rC, 32]` is not in `tx.inputs`

### `BHEI`: Block height

|             |                            |
|-------------|----------------------------|
| Description | Get Fuel block height.     |
| Operation   | ```$rA = blockheight();``` |
| Syntax      | `bhei $rA`                 |
| Encoding    | `0x00 rA - - -`            |
| Notes       |                            |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)

### `BHSH`: Block hash

|             |                                      |
|-------------|--------------------------------------|
| Description | Get block header hash.               |
| Operation   | ```MEM[$rA, 32] = blockhash($rB);``` |
| Syntax      | `bhsh $rA $rB`                       |
| Encoding    | `0x00 rA rB - -`                     |
| Notes       |                                      |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)

Block header hashes for blocks with height greater than or equal to current block height are zero (```0x00**32```).

### `BURN`: Burn existing coins

|             |                                                             |
|-------------|-------------------------------------------------------------|
| Description | Burn `$rA` coins of the `$rB` ID from the current contract. |
| Operation   | ```burn($rA, $rB);```                                       |
| Syntax      | `burn $rA $rB`                                              |
| Encoding    | `0x00 rA rB - -`                                            |
| Notes       | `$rB` is a pointer to a 32 byte ID in memory.               |

The asset ID is constructed using the asset ID construction method.

Panic if:

- `$rB + 32` overflows or `> VM_MAX_RAM`
- Balance of asset ID from `constructAssetID(MEM[$fp, 32], MEM[$rB, 32])` of output with contract ID `MEM[$fp, 32]` minus `$rA` underflows
- `$fp == 0` (in the script context)

For output with contract ID `MEM[$fp, 32]`, decrease balance of asset ID `constructAssetID(MEM[$fp, 32], MEM[$rB, 32])` by `$rA`.

This modifies the `balanceRoot` field of the appropriate output.

Append a receipt to the list of receipts:

| name          | type          | description                                |
|---------------|---------------|--------------------------------------------|
| `type`        | `ReceiptType` | `ReceiptType.Burn`                         |
| `sub_id`      | `byte[32]`    | Asset sub identifier `MEM[$rB, $rB + 32]`. |
| `contract_id` | `byte[32]`    | Contract ID of the current context.        |
| `val`         | `uint64`      | Value of register `$rA`.                   |
| `pc`          | `uint64`      | Value of register `$pc`.                   |
| `is`          | `uint64`      | Value of register `$is`.                   |

### `CALL`: Call contract

|             |                        |
|-------------|------------------------|
| Description | Call contract.         |
| Operation   |                        |
| Syntax      | `call $rA $rB $rC $rD` |
| Encoding    | `0x00 rA rB rC rD`     |
| Effects     | External call          |
| Notes       |                        |

There is a `balanceOfStart(asset_id: byte[32]) -> uint32` helper that returns the memory address of the remaining free balance of `asset_id`. If `asset_id` has no free balance remaining, the helper panics.

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
- Reading past `MEM[VM_MAX_RAM - 1]`
- In an external context, if `$rB > MEM[balanceOfStart(MEM[$rC, 32]), 8]`
- In an internal context, if `$rB` is greater than the balance of asset ID `MEM[$rC, 32]` of output with contract ID `MEM[$fp, 32]`

Register `$rA` is a memory address from which the following fields are set (word-aligned):

| bytes | type       | value    | description          |
|-------|------------|----------|----------------------|
| 32    | `byte[32]` | `to`     | Contract ID to call. |
| 8     | `byte[8]`  | `param1` | First parameter.     |
| 8     | `byte[8]`  | `param2` | Second parameter.    |

`$rB` is the amount of coins to forward. `$rC` points to the 32-byte asset ID of the coins to forward. `$rD` is the amount of gas to forward. If it is set to an amount greater than the available gas, all available gas is forwarded.

Append a receipt to the list of receipts:

| name       | type          | description                                                               |
|------------|---------------|---------------------------------------------------------------------------|
| `type`     | `ReceiptType` | `ReceiptType.Call`                                                        |
| `from`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `to`       | `byte[32]`    | Contract ID of called contract.                                           |
| `amount`   | `uint64`      | Amount of coins to forward, i.e. `$rB`.                                   |
| `asset_id` | `byte[32]`    | Asset ID of coins to forward, i.e. `MEM[$rC, 32]`.                        |
| `gas`      | `uint64`      | Gas to forward, i.e. `min($rD, $cgas)`.                                   |
| `param1`   | `uint64`      | First parameter.                                                          |
| `param2`   | `uint64`      | Second parameter.                                                         |
| `pc`       | `uint64`      | Value of register `$pc`.                                                  |
| `is`       | `uint64`      | Value of register `$is`.                                                  |

For output with contract ID `MEM[$rA, 32]`, increase balance of asset ID `MEM[$rC, 32]` by `$rB`. In an external context, decrease `MEM[balanceOfStart(MEM[$rC, 32]), 8]` by `$rB`. In an internal context, decrease asset ID `MEM[$rC, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rB`.

A [call frame](./index.md#call-frames) is pushed at `$sp`. In addition to filling in the values of the call frame, the following registers are set:

1. `$fp = $sp` (on top of the previous call frame is the beginning of this call frame)
1. Set `$ssp` and `$sp` to the start of the writable stack area of the call frame.
1. Set `$pc` and `$is` to the starting address of the code.
1. `$flag` set to zero.
1. `$bal = $rB` (forward coins)
1. `$cgas = $rD` or all available gas (forward gas)

This modifies the `balanceRoot` field of the appropriate output(s).

### `CB`: Coinbase contract id

|             |                                                                                                                     |
|-------------|---------------------------------------------------------------------------------------------------------------------|
| Description | Get the [coinbase contract id](../protocol/tx-validity.md#coinbase-transaction) associated with the block proposer. |
| Operation   | ```MEM[$rA, 32] = coinbase();```                                                                                    |
| Syntax      | `cb $rA`                                                                                                            |
| Encoding    | `0x00 rA - - -`                                                                                                     |
| Notes       |                                                                                                                     |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)

### `CCP`: Code copy

|             |                                                                                                                                                  |
|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| Description | Copy `$rD` bytes of code starting at `$rC` for contract with ID equal to the 32 bytes in memory starting at `$rB` into memory starting at `$rA`. |
| Operation   | ```MEM[$rA, $rD] = code($rB, $rC, $rD);```                                                                                                       |
| Syntax      | `ccp $rA, $rB, $rC, $rD`                                                                                                                         |
| Encoding    | `0x00 rA rB rC rD`                                                                                                                               |
| Notes       | If `$rD` is greater than the code size, zero bytes are filled in.                                                                                |

This is used only for reading and inspecting code of other contracts.
Use [`LDC`](#ldc-load-code-from-an-external-contract-blob-or-memory) to load code for executing.

Panic if:

- `$rA + $rD` overflows or `> VM_MAX_RAM`
- `$rB + 32` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, $rD]`  does not pass [ownership check](./index.md#ownership)
- Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

### `CROO`: Code Merkle root

|             |                                                                                                                                       |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Description | Set the 32 bytes in memory starting at `$rA` to the code root for contract with ID equal to the 32 bytes in memory starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = coderoot(MEM[$rB, 32]);```                                                                                          |
| Syntax      | `croo $rA, $rB`                                                                                                                       |
| Encoding    | `0x00 rA rB - -`                                                                                                                      |
| Notes       |                                                                                                                                       |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB + 32` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)
- Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

Code root computation is defined [here](../identifiers/contract-id.md).

### `CSIZ`: Code size

|             |                                                                                                           |
|-------------|-----------------------------------------------------------------------------------------------------------|
| Description | Set `$rA` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rB`. |
| Operation   | ```$rA = codesize(MEM[$rB, 32]);```                                                                       |
| Syntax      | `csiz $rA, $rB`                                                                                           |
| Encoding    | `0x00 rA rB - -`                                                                                          |
| Notes       |                                                                                                           |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

### `LDC`: Load code from an external contract, blob or memory

|             |                                                                                                                                                   |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| Description | Copy `$rC` bytes of code at offset `$rB` from object identified with `$rA` into memory starting at `$ssp`. Object type is in `imm`.               |
| Operation   | `code = match imm { 0 => contract_code(mem[$rA,32]), 1 => blob_payload(mem[$rA,32]), 2 => mem[$ra, ..] }; MEM[$ssp, $rC] = code[$rB, $rC];`       |
| Syntax      | `ldc $rA, $rB, $rC, imm`                                                                                                                          |
| Encoding    | `0x00 rA rB rC imm`                                                                                                                               |
| Notes       | If `$rC` is greater than the code size, zero bytes are filled in. Final length is always padded to word boundary.                                 |

Object type from `imm` determines the source for loading as follows:

| `imm` | Object type   |
|-------|---------------|
| `0`   | Contract code |
| `1`   | Blob payload  |
| `2`   | VM memory     |
| other | _reserved_    |

Panic if:

- `$ssp + $rC` overflows or `> VM_MAX_RAM`
- `imm <= 1` and `$rA + 32` overflows or `> VM_MAX_RAM`
- `$ssp + $rC >= $hp`
- `imm == 0` and `$rC > CONTRACT_MAX_SIZE`
- `imm == 0` and contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
- `imm == 0` and context is a predicate
- `imm == 1` and blob with ID `MEM[$rA, 32]` is not found in the chain state
- `imm == 2` and `$rA + $rB + $rC` overflows or `> VM_MAX_RAM`
- `imm >= 3` (reserved value)

Increment `$fp->codesize`, `$ssp` by `$rC` padded to word alignment. Then set `$sp` to `$ssp`.

This instruction can be used to concatenate the code of multiple contracts or blobs together. It can only be used when the stack area of the call frame is zero-sized.

### `LOG`: Log event

|             |                                |
|-------------|--------------------------------|
| Description | Log an event. This is a no-op. |
| Operation   | ```log($rA, $rB, $rC, $rD);``` |
| Syntax      | `log $rA, $rB, $rC, $rD`       |
| Encoding    | `0x00 rA rB rC rD`             |
| Notes       |                                |

Append a receipt to the list of receipts:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Log`                                                         |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val0` | `uint64`      | Value of register `$rA`.                                                  |
| `val1` | `uint64`      | Value of register `$rB`.                                                  |
| `val2` | `uint64`      | Value of register `$rC`.                                                  |
| `val3` | `uint64`      | Value of register `$rD`.                                                  |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

### `LOGD`: Log data event

|             |                                 |
|-------------|---------------------------------|
| Description | Log an event. This is a no-op.  |
| Operation   | ```logd($rA, $rB, $rC, $rD);``` |
| Syntax      | `logd $rA, $rB, $rC, $rD`       |
| Encoding    | `0x00 rA rB rC rD`              |
| Notes       |                                 |

Append a receipt to the list of receipts:

| name     | type          | description                                                               |
|----------|---------------|---------------------------------------------------------------------------|
| `type`   | `ReceiptType` | `ReceiptType.LogData`                                                     |
| `id`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val0`   | `uint64`      | Value of register `$rA`.                                                  |
| `val1`   | `uint64`      | Value of register `$rB`.                                                  |
| `ptr`    | `uint64`      | Value of register `$rC`.                                                  |
| `len`    | `uint64`      | Value of register `$rD`.                                                  |
| `digest` | `byte[32]`    | [Hash](#s256-sha-2-256) of `MEM[$rC, $rD]`.                               |
| `pc`     | `uint64`      | Value of register `$pc`.                                                  |
| `is`     | `uint64`      | Value of register `$is`.                                                  |

Logs the memory range `MEM[$rC, $rD]`.

Panics if:

- `$rC + $rD` overflows or `> VM_MAX_RAM`

### `MINT`: Mint new coins

|             |                                                             |
|-------------|-------------------------------------------------------------|
| Description | Mint `$rA` coins of the `$rB` ID from the current contract. |
| Operation   | ```mint($rA, $rB);```                                       |
| Syntax      | `mint $rA $rB`                                              |
| Encoding    | `0x00 rA rB - -`                                            |
| Notes       | `$rB` is a pointer to a 32 byte ID in memory                |

The asset ID will be constructed using the asset ID construction method.

Panic if:

- `$rB + 32` overflows or `> VM_MAX_RAM`
- Balance of asset ID `constructAssetID(MEM[$fp, 32], MEM[$rB])` of output with contract ID `MEM[$fp, 32]` plus `$rA` overflows
- `$fp == 0` (in the script context)

For output with contract ID `MEM[$fp, 32]`, increase balance of asset ID `constructAssetID(MEM[$fp, 32], MEM[$rB])` by `$rA`.

This modifies the `balanceRoot` field of the appropriate output.

Append a receipt to the list of receipts:

| name          | type          | description                                |
|---------------|---------------|--------------------------------------------|
| `type`        | `ReceiptType` | `ReceiptType.Mint`                         |
| `sub_id`      | `byte[32]`    | Asset sub identifier `MEM[$rB, $rB + 32]`. |
| `contract_id` | `byte[32]`    | Contract ID of the current context.        |
| `val`         | `uint64`      | Value of register `$rA`.                   |
| `pc`          | `uint64`      | Value of register `$pc`.                   |
| `is`          | `uint64`      | Value of register `$is`.                   |

### `RETD`: Return from context with data

|             |                                                                         |
|-------------|-------------------------------------------------------------------------|
| Description | Returns from [context](./index.md#contexts) with value `MEM[$rA, $rB]`. |
| Operation   | ```returndata($rA, $rB);```                                             |
| Syntax      | `retd $rA, $rB`                                                         |
| Encoding    | `0x00 rA rB - -`                                                        |
| Notes       | `$ret` is set to the pointer `$rA`, and `$retl` to length `$rB`.        |

Panic if:

- `$rA + $rB` overflows or `> VM_MAX_RAM`

Append a receipt to the list of receipts:

| name     | type          | description                                                               |
|----------|---------------|---------------------------------------------------------------------------|
| `type`   | `ReceiptType` | `ReceiptType.ReturnData`                                                  |
| `id`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `ptr`    | `uint64`      | Value of register `$rA`.                                                  |
| `len`    | `uint64`      | Value of register `$rB`.                                                  |
| `digest` | `byte[32]`    | [Hash](#s256-sha-2-256) of `MEM[$rA, $rB]`.                               |
| `pc`     | `uint64`      | Value of register `$pc`.                                                  |
| `is`     | `uint64`      | Value of register `$is`.                                                  |

If current context is a script, append an additional receipt to the list of receipts:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `0`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

If current context is external, cease VM execution and return `MEM[$rA, $rB]`.

Returns from contract call, popping the call frame. Before popping, perform the following operations.

Return the unused forwarded gas to the caller:

1. `$cgas = $cgas + $fp->$cgas` (add remaining context gas from previous context to current remaining context gas)

Set the return value:

1. `$ret = $rA`
1. `$retl = $rB`

Then pop the call frame and restore all registers _except_ `$ggas`, `$cgas`, `$ret`, `$retl` and `$hp`. Afterwards, set the following registers:

1. `$pc = $pc + 4` (advance program counter from where we called)

### `RVRT`: Revert

|             |                                                                       |
|-------------|-----------------------------------------------------------------------|
| Description | Halt execution, reverting state changes and returning value in `$rA`. |
| Operation   | ```revert($rA);```                                                    |
| Syntax      | `rvrt $rA`                                                            |
| Encoding    | `0x00 rA - - -`                                                       |
| Notes       |                                                                       |

Append a receipt to the list of receipts:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Revert`                                                      |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val`  | `uint64`      | Value of register `$rA`.                                                  |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

Then append an additional receipt to the list of receipts:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `1`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

Cease VM execution and revert script effects. After a revert:

1. All [`OutputContract`](../tx-format/output.md#outputcontract) outputs will have the same `balanceRoot` and `stateRoot` as on initialization.
1. All [`OutputVariable`](../tx-format/output.md#outputvariable) outputs will have `to`, `amount`, and `asset_id` of zero.

### `SMO`: Send message out

|             |                                                                                                                                                               |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description | Send a message to recipient address `MEM[$rA, 32]` from the `MEM[$fp, 32]` sender with message data `MEM[$rB, $rC]` and the `$rD` amount of base asset coins. |
| Operation   | ```outputmessage(MEM[$fp, 32], MEM[$rA, 32], MEM[$rB, $rC], $rD);```                                                                                          |
| Syntax      | `smo $rA, $rB, $rC, $rD`                                                                                                                                      |
| Encoding    | `0x00 rA rB rC rD`                                                                                                                                            |
| Effects     | Output message                                                                                                                                                |
| Notes       |                                                                                                                                                               |

There is a `balanceOfStart(asset_id: byte[32]) -> uint32` helper that returns the memory address of the remaining free balance of `asset_id`. If `asset_id` has no free balance remaining, the helper panics.

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB + $rC` overflows or `> VM_MAX_RAM`
- `$rC > MESSAGE_MAX_DATA_SIZE`
- In an external context, if `$rD > MEM[balanceOfStart(0), 8]`
- In an internal context, if `$rD` is greater than the balance of asset ID 0 of output with contract ID `MEM[$fp, 32]`

Append a receipt to the list of receipts:

| name        | type          | description                                                                     |
|-------------|---------------|---------------------------------------------------------------------------------|
| `type`      | `ReceiptType` | `ReceiptType.MessageOut`                                                        |
| `sender`    | `byte[32]`    | The address of the message sender: `MEM[$fp, 32]`.                              |
| `recipient` | `byte[32]`    | The address of the message recipient: `MEM[$rA, 32]`.                           |
| `amount`    | `uint64`      | Amount of base asset coins sent with message: `$rD`.                            |
| `nonce`     | `byte[32]`    | The message nonce as described [here](../identifiers/utxo-id.md#message-nonce). |
| `len`       | `uint64`      | Length of message data, in bytes: `$rC`.                                        |
| `digest`    | `byte[32]`    | [Hash](#s256-sha-2-256) of `MEM[$rB, $rC]`.                                     |

In an external context, decrease `MEM[balanceOfStart(0), 8]` by `$rD`. In an internal context, decrease asset ID 0 balance of output with contract ID `MEM[$fp, 32]` by `$rD`. This modifies the `balanceRoot` field of the appropriate contract that had its' funds deducted.

### `SCWQ`: State clear sequential 32 byte slots

|             |                                                                               |
|-------------|-------------------------------------------------------------------------------|
| Description | A sequential series of 32 bytes is cleared from the current contract's state. |
| Operation   | ```STATE[MEM[$rA, 32], 32 * $rC] = None;```                                   |
| Syntax      | `scwq $rA, $rB, $rC`                                                          |
| Encoding    | `0x00 rA rB rC -`                                                             |
| Notes       |                                                                               |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB` is a [reserved register](./index.md#semantics)
- `$fp == 0` (in the script context)

Register `$rB` will be set to `false` if any storage slot in the requested range was already unset (default) and `true` if all the slots were set.

### `SRW`: State read word

|             |                                                   |
|-------------|---------------------------------------------------|
| Description | A word is read from the current contract's state. |
| Operation   | ```$rA = STATE[MEM[$rC, 32]][0, 8];```            |
| Syntax      | `srw $rA, $rB, $rC`                               |
| Encoding    | `0x00 rA rB rC -`                                 |
| Effects     | Storage read                                      |
| Notes       | Returns zero if the state element does not exist. |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB` is a [reserved register](./index.md#semantics)
- `$rC + 32` overflows or `> VM_MAX_RAM`
- `$fp == 0` (in the script context)

Register `$rB` will be set to `false` if the requested slot is unset (default) and `true` if it's set.

### `SRWQ`: State read sequential 32 byte slots

|             |                                                                            |
|-------------|----------------------------------------------------------------------------|
| Description | A sequential series of 32 bytes is read from the current contract's state. |
| Operation   | ```MEM[$rA, 32 * rD] = STATE[MEM[$rC, 32], 32 * rD];```                    |
| Syntax      | `srwq $rA, $rB, $rC, $rD`                                                  |
| Encoding    | `0x00 rA rB rC rD`                                                         |
| Effects     | Storage read                                                               |
| Notes       | Returns zero if the state element does not exist.                          |

Panic if:

- `$rA + 32 * rD` overflows or `> VM_MAX_RAM`
- `$rC + 32 * rD` overflows or `> VM_MAX_RAM`
- `$rB` is a [reserved register](./index.md#semantics)
- The memory range `MEM[$rA, 32 * rD]`  does not pass [ownership check](./index.md#ownership)
- `$fp == 0` (in the script context)

Register `$rB` will be set to `false` if any storage slot in the requested range is unset (default) and `true` if all the slots are set.

### `SWW`: State write word

|             |                                                                                 |
|-------------|---------------------------------------------------------------------------------|
| Description | A word is written to the current contract's state.                              |
| Operation   | ```STATE[MEM[$rA, 32]][0, 8] = $rC;```<br>```STATE[MEM[$rA, 32]][8, 24] = 0;``` |
| Syntax      | `sww $rA $rB $rC`                                                               |
| Encoding    | `0x00 rA rB rC -`                                                               |
| Effects     | Storage write                                                                   |
| Notes       | Additional gas is charged when a new storage slot is created.                   |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB` is a [reserved register](./index.md#semantics)
- `$fp == 0` (in the script context)

The last 24 bytes of `STATE[MEM[$rA, 32]]` are set to `0`. Register `$rB` will be set to the number of new slots written, i.e. `1` if the slot was previously unset, and `0` if it already contained a value.

### `SWWQ`: State write sequential 32 byte slots

|             |                                                                             |
|-------------|-----------------------------------------------------------------------------|
| Description | A sequential series of 32 bytes is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rA, 32], 32 * $rD] = MEM[$rC, 32 * $rD];```                   |
| Syntax      | `swwq $rA, $rB, $rC, $rD`                                                   |
| Encoding    | `0x00 rA rB rC rD`                                                          |
| Effects     | Storage write                                                               |
| Notes       | Additional gas is charged when for each new storage slot created.           |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32 * $rD` overflows or `> VM_MAX_RAM`
- `$rB` is a [reserved register](./index.md#semantics)
- `$fp == 0` (in the script context)

Register `$rB` will be set to the number of storage slots that were previously unset, and were set by this operation.

### `TIME`: Timestamp at height

|             |                                         |
|-------------|-----------------------------------------|
| Description | Get timestamp of block at given height. |
| Operation   | ```$rA = time($rB);```                  |
| Syntax      | `time $rA, $rB`                         |
| Encoding    | `0x00 rA rB - -`                        |
| Notes       |                                         |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB` is greater than the current block height.

Gets the timestamp of the block at height `$rB`. Time is in [TAI64](https://cr.yp.to/libtai/tai64.html) format.

### `TR`: Transfer coins to contract

|             |                                                                           |
|-------------|---------------------------------------------------------------------------|
| Description | Transfer `$rB` coins with asset ID at `$rC` to contract with ID at `$rA`. |
| Operation   | ```transfer(MEM[$rA, 32], $rB, MEM[$rC, 32]);```                          |
| Syntax      | `tr $rA, $rB, $rC`                                                        |
| Encoding    | `0x00 rA rB rC -`                                                         |
| Effects     | Balance tree read, balance tree write                                     |
| Notes       |                                                                           |

There is a `balanceOfStart(asset_id: byte[32]) -> uint32` helper that returns the memory address of the remaining free balance of `asset_id`. If `asset_id` has no free balance remaining, the helper panics.

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
- In an external context, if `$rB > MEM[balanceOfStart(MEM[$rC, 32]), 8]`
- In an internal context, if `$rB` is greater than the balance of asset ID `MEM[$rC, 32]` of output with contract ID `MEM[$fp, 32]`
- `$rB == 0`

Append a receipt to the list of receipts:

| name       | type          | description                                                               |
|------------|---------------|---------------------------------------------------------------------------|
| `type`     | `ReceiptType` | `ReceiptType.Transfer`                                                    |
| `from`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `to`       | `byte[32]`    | Contract ID of contract to transfer coins to.                             |
| `amount`   | `uint64`      | Amount of coins transferred.                                              |
| `asset_id` | `byte[32]`    | asset ID of coins transferred.                                            |
| `pc`       | `uint64`      | Value of register `$pc`.                                                  |
| `is`       | `uint64`      | Value of register `$is`.                                                  |

For output with contract ID `MEM[$rA, 32]`, increase balance of asset ID `MEM[$rC, 32]` by `$rB`. In an external context, decrease `MEM[balanceOfStart(MEM[$rC, 32]), 8]` by `$rB`. In an internal context, decrease asset ID `MEM[$rC, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rB`.

This modifies the `balanceRoot` field of the appropriate output(s).

### `TRO`: Transfer coins to output

|             |                                                                                     |
|-------------|-------------------------------------------------------------------------------------|
| Description | Transfer `$rC` coins with asset ID at `$rD` to address at `$rA`, with output `$rB`. |
| Operation   | ```transferout(MEM[$rA, 32], $rB, $rC, MEM[$rD, 32]);```                            |
| Syntax      | `tro $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                  |
| Effects     | Balance tree read, balance tree write                                               |
| Notes       |                                                                                     |

There is a `balanceOfStart(asset_id: byte[32]) -> uint32` helper that returns the memory address of the remaining free balance of `asset_id`. If `asset_id` has no free balance remaining, the helper panics.

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rD + 32` overflows or `> VM_MAX_RAM`
- `$rB > tx.outputsCount`
- In an external context, if `$rC > MEM[balanceOfStart(MEM[$rD, 32]), 8]`
- In an internal context, if `$rC` is greater than the balance of asset ID `MEM[$rD, 32]` of output with contract ID `MEM[$fp, 32]`
- `$rC == 0`
- `tx.outputs[$rB].type != OutputType.Variable`
- `tx.outputs[$rB].amount != 0`

Append a receipt to the list of receipts:

| name       | type          | description                                                               |
|------------|---------------|---------------------------------------------------------------------------|
| `type`     | `ReceiptType` | `ReceiptType.TransferOut`                                                 |
| `from`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `to`       | `byte[32]`    | Address to transfer coins to.                                             |
| `amount`   | `uint64`      | Amount of coins transferred.                                              |
| `asset_id` | `byte[32]`    | asset ID of coins transferred.                                            |
| `pc`       | `uint64`      | Value of register `$pc`.                                                  |
| `is`       | `uint64`      | Value of register `$is`.                                                  |

In an external context, decrease `MEM[balanceOfStart(MEM[$rD, 32]), 8]` by `$rC`. In an internal context, decrease asset ID `MEM[$rD, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rC`. Then set:

- `tx.outputs[$rB].to = MEM[$rA, 32]`
- `tx.outputs[$rB].amount = $rC`
- `tx.outputs[$rB].asset_id = MEM[$rD, 32]`

This modifies the `balanceRoot` field of the appropriate output(s).

## Blob Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### `BSIZ`: Blob size

|             |                                                                                                           |
|-------------|-----------------------------------------------------------------------------------------------------------|
| Description | Set `$rA` to the size of the blob with ID equal to the 32 bytes in memory starting at `$rB`.              |
| Operation   | `$rA = len(blob(MEM[$rB, 32]));`                                                                          |
| Syntax      | `bsiz $rA, $rB`                                                                                           |
| Encoding    | `0x00 rA rB - -`                                                                                          |
| Notes       |                                                                                                           |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `$rB + 32` overflows or `> VM_MAX_RAM`
- Blob ID `MEM[$rB, 32]` is not found

### `BLDD`: Load data from a blob

|             |                                                                                                             |
|-------------|-------------------------------------------------------------------------------------------------------------|
| Description | Load 32-byte blob id at `$rB`, and copy `$rD` bytes starting from `$rC` into `$sA`.                         |
| Operation   | `MEM[$rA, $rD] = blob(MEM[$rB, 32])[$rC, $rD];`                                                             |
| Syntax      | `bldd $rA, $rB, rC, $rD`                                                                                    |
| Encoding    | `0x00 rA rB rC rD`                                                                                          |
| Notes       | If `$rC >` blob size, zero bytes are filled in.                                                             |

Panic if:

- `$rA + $rD` overflows or `> VM_MAX_RAM` or `> $hp`
- `$rB + 32` overflows or `> VM_MAX_RAM`
- Blob ID `MEM[$rB, 32]` is not found

## Cryptographic Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### `ECK1`: Secp256k1 signature recovery

|             |                                                                                                                             |
|-------------|-----------------------------------------------------------------------------------------------------------------------------|
| Description | The 64-byte public key (x, y) recovered from 64-byte signature starting at `$rB` on 32-byte message hash starting at `$rC`. |
| Operation   | ```MEM[$rA, 64] = ecrecover_k1(MEM[$rB, 64], MEM[$rC, 32]);```                                                              |
| Syntax      | `eck1 $rA, $rB, $rC`                                                                                                        |
| Encoding    | `0x00 rA rB rC -`                                                                                                           |
| Notes       | Takes message hash as an input. You can use `S256` to hash the message if needed.                                           |

Panic if:

- `$rA + 64` overflows or `> VM_MAX_RAM`
- `$rB + 64` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 64]` does not pass [ownership check](./index.md#ownership)

Signatures and signature verification are specified [here](../protocol/cryptographic-primitives.md#ecdsa-public-key-cryptography).

If the signature cannot be verified, `MEM[$rA, 64]` is set to `0` and `$err` is set to `1`, otherwise `$err` is cleared.

To get the address from the public key, hash the public key with [SHA-2-256](../protocol/cryptographic-primitives.md#hashing).

### `ECR1`: Secp256r1 signature recovery

|             |                                                                                                                             |
|-------------|-----------------------------------------------------------------------------------------------------------------------------|
| Description | The 64-byte public key (x, y) recovered from 64-byte signature starting at `$rB` on 32-byte message hash starting at `$rC`. |
| Operation   | ```MEM[$rA, 64] = ecrecover_r1(MEM[$rB, 64], MEM[$rC, 32]);```                                                              |
| Syntax      | `ecr1 $rA, $rB, $rC`                                                                                                        |
| Encoding    | `0x00 rA rB rC -`                                                                                                           |
| Notes       | Takes message hash as an input. You can use `S256` to hash the message if needed.                                           |

Panic if:

- `$rA + 64` overflows or `> VM_MAX_RAM`
- `$rB + 64` overflows or `> VM_MAX_RAM`
- `$rC + 32` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 64]` does not pass [ownership check](./index.md#ownership)

Signatures and signature verification are specified [here](../protocol/cryptographic-primitives.md#ecdsa-public-key-cryptography).

If the signature cannot be verified, `MEM[$rA, 64]` is set to `0` and `$err` is set to `1`, otherwise `$err` is cleared.

To get the address from the public key, hash the public key with [SHA-2-256](../protocol/cryptographic-primitives.md#hashing).

### `ED19`: EdDSA curve25519 verification

|             |                                                                                                                             |
|-------------|-----------------------------------------------------------------------------------------------------------------------------|
| Description | Verification 64-byte signature at `$rB` with 32-byte public key at `$rA` for a message starting at `$rC` with length `$rD`. |
| Operation   | ```ed19verify(MEM[$rA, 32], MEM[$rB, 64], MEM[$rC, $rD]);```                                                                |
| Syntax      | `ed19 $rA, $rB, $rC, $rD`                                                                                                   |
| Encoding    | `0x00 rA rB rC rD`                                                                                                          |
| Notes       | Takes message instead of hash. **For backwards compatibility reasons, if `$rD == 0`, it will be treated as `32`.**          |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB + 64` overflows or `> VM_MAX_RAM`
- `$rC + $rD` overflows or `> VM_MAX_RAM`

Verification are specified [here](../protocol/cryptographic-primitives.md#eddsa-public-key-cryptography).

If there is an error in verification, `$err` is set to `1`, otherwise `$err` is cleared.

### `K256`: keccak-256

|             |                                                       |
|-------------|-------------------------------------------------------|
| Description | The keccak-256 hash of `$rC` bytes starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = keccak256(MEM[$rB, $rC]);```        |
| Syntax      | `k256 $rA, $rB, $rC`                                  |
| Encoding    | `0x00 rA rB rC -`                                     |
| Notes       |                                                       |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB + $rC` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)

### `S256`: SHA-2-256

|             |                                                      |
|-------------|------------------------------------------------------|
| Description | The SHA-2-256 hash of `$rC` bytes starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = sha256(MEM[$rB, $rC]);```          |
| Syntax      | `s256 $rA, $rB, $rC`                                 |
| Encoding    | `0x00 rA rB rC -`                                    |
| Notes       |                                                      |

Panic if:

- `$rA + 32` overflows or `> VM_MAX_RAM`
- `$rB + $rC` overflows or `> VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./index.md#ownership)

### `ECOP`: Elliptic curve point operation

|             |                                                     |
|-------------|-----------------------------------------------------|
| Description | Perform arithmetic operation `$rC` on points of the elliptic curve `$rB`. Arguments are read from memory at `$rD`, and result is written to the memory at `$rA`, as per the table below.                                                   |
| Operation   | ```MEM[$rA, X] = ecop(MEM[$rD, Y]);```              |
| Syntax      | `ecop $rA, $rB, $rC, $rD`                           |
| Encoding    | `0x00 rA rB rC rD`                                  |
| Notes       | For now, only `$rB` = 0 is accepted                 |

#### Curve ID `$rB` possible values

- `0`: `alt_bn128` elliptic curve.

#### Operation type `$rC` supported

- `0`: two points addition
- `1`: one point and one scalar multiplication

#### Encoding of points and results by curve ID and operation type

- 1P = one point = (X, Y) = ([32 bytes], [32 bytes])
- 1S = one scalar = X = [32 bytes]

| `$rB` Curve ID | `$rC` Operation type | `$rA` format         | `$rD` format               |
|----------------|----------------------|----------------------|----------------------------|
|    `0`         | `0`                  | `MEM[$rA, 64]` `1P`  | `MEM[$rD, 128]` `1P1P`     |
|    `0`         | `1`                  | `MEM[$rA, 64]` `1P`  | `MEM[$rD, 96]` `1P1S`      |

#### Panic cases

- Curve ID is not supported (`$rB`)
- Operation type is not supported (`$rC`)
- `$rD` + (size depending on the table above) overflows or `> VM_MAX_RAM`
- Decoding of `$rD` memory doesn't match the expected format described above for each case.
- The memory range at `$rA` (size depending on the curve/operation types) does not pass [ownership check](./index.md#ownership)

### `EPAR`: Elliptic curve point pairing check

|             |                                                     |
|-------------|-----------------------------------------------------|
| Description | Check if `$rC` groups of points at `$rD` all form valid pairings in (curve, pairing type) identified by `$rB`. Set `$rA` to the result of the pairing, either `0` or `1`. |
| Operation   | ```$rA = epar(MEM[$rD, X * $rC]);```                |
| Syntax      | `epar $rA, $rB, $rC, $rD`                           |
| Encoding    | `0x00 rA rB rC rD`                                  |
| Notes       | For now, only `$rB` = 0 is accepted.                |

<!-- markdownlint-disable-next-line no-duplicate-header -->
#### Curve/Pairing ID `$rB` possible values

- `0`: optimal ate pairing on `alt_bn128` elliptic curve.

#### Encoding of points by curve ID and check type

- 1P = one point = (X, Y) = ([32 bytes], [32 bytes])

| `$rB` Curve / Pairing ID  | `$rD` format               |
|---------------------------|----------------------------|
|    `0`                    | `MEM[$rD, (64 + 64 + 64) * $rC]` Each element is `1P1P1P` (three points coordinates) (192 bytes)   |

<!-- markdownlint-disable-next-line no-duplicate-header -->
#### Panic cases

- Curve ID/Pairing is not supported (`$rB`)
- `$rD` has elements than described in `$rC`
- `$rD` + (size depending on the table above) overflows or `> VM_MAX_RAM`
- Decoding of `$rD` memory doesn't match the expected format described above for each case.
- The memory range at `$rA` (size depending on the curve/operation types) does not pass [ownership check](./index.md#ownership)

## Other Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### `ECAL`: Call external function

|             |                                                                          |
|-------------|--------------------------------------------------------------------------|
| Description | Call an external function that has full access to the VM state.          |
| Operation   | `external(&mut vm, $rA, $rB, $rC, $rD)`                                  |
| Syntax      | `ecal $rA $rB $rC $rD`                                                   |
| Encoding    | `0x00 rA rB rC rD`                                                       |
| Notes       | Does nothing by default, but the VM user can define this to do anything. |

This function provides an escape hatch from the VM, similar to `ecall` instruction of RISC-V. The suggested convention is to use `$rA` for "system call number", i.e. identifying the procedure to call, but all arguments can be used freely. The operation can modify the VM state freely, including writing to registers and memory. Again, the suggested convention is to use `$rA` for the return value and `$err` for any possible errors. However, these conventions can be ignored when necessary.

Panic if:

- The external function panics.

### `FLAG`: Set flags

|             |                       |
|-------------|-----------------------|
| Description | Set `$flag` to `$rA`. |
| Operation   | ```$flag = $rA;```    |
| Syntax      | `flag $rA`            |
| Encoding    | `0x00 rA - - -`       |
| Notes       |                       |

Panic if:

- Any reserved flags are set

### `GM`: Get metadata

|             |                           |
|-------------|---------------------------|
| Description | Get metadata from memory. |
| Operation   | Varies (see below).       |
| Syntax      | `gm $rA, imm`             |
| Encoding    | `0x00 rA imm imm imm`     |
| Notes       |                           |

Read metadata from memory. A convenience instruction to avoid manually extracting metadata.

| name                         | value     | description                      |
|------------------------------|-----------|----------------------------------|
| `GM_IS_CALLER_EXTERNAL`      | `0x00001` | Get if caller is external.       |
| `GM_GET_CALLER`              | `0x00002` | Get caller's contract ID.        |
| `GM_GET_VERIFYING_PREDICATE` | `0x00003` | Get index of current predicate.  |
| `GM_GET_CHAIN_ID`            | `0x00004` | Get the value of `CHAIN_ID`      |
| `GM_TX_START`                | `0x00005` | Transaction start memory address |
| `GM_BASE_ASSET_ID`           | `0x00006` | Base asset ID                    |

If `imm == GM_IS_CALLER_EXTERNAL`:

Panic if:

- `$fp == 0` (in an external context)

Set `$rA` to `true` if parent is an external context, `false` otherwise.

If `imm == GM_GET_CALLER`:

Panic if:

- `$fp == 0` (in an external context)
- `$fp->$fp == 0` (if parent context is external)

Set `$rA` to `$fp->$fp` (i.e. `$rA` will point to the previous call frame's contract ID).

If `imm == GM_GET_VERIFYING_PREDICATE`:

Panic if:

- not in a predicate context

Set `$rA` to the index of the currently-verifying predicate.

### `GTF`: Get transaction fields

|             |                                         |
|-------------|-----------------------------------------|
| Description | Get transaction fields.                 |
| Operation   | Set `$rA` according to the table below. |
| Syntax      | `gtf $rA, $rB, imm`                     |
| Encoding    | `0x00 rA rB i i`                        |
| Notes       | `$rB` is ignored for many variants.     |

Get [fields from the transaction](../tx-format/transaction.md).

| name                                      | `imm`   | set `$rA` to                                                      |
|-------------------------------------------|---------|-------------------------------------------------------------------|
| `GTF_TYPE`                                | `0x001` | `tx.type`                                                         |
| `GTF_SCRIPT_GAS_LIMIT`                    | `0x002` | `tx.scriptGasLimit`                                               |
| `GTF_SCRIPT_SCRIPT_LENGTH`                | `0x003` | `tx.scriptLength`                                                 |
| `GTF_SCRIPT_SCRIPT_DATA_LENGTH`           | `0x004` | `tx.scriptDataLength`                                             |
| `GTF_SCRIPT_INPUTS_COUNT`                 | `0x005` | `tx.inputsCount`                                                  |
| `GTF_SCRIPT_OUTPUTS_COUNT`                | `0x006` | `tx.outputsCount`                                                 |
| `GTF_SCRIPT_WITNESSES_COUNT`              | `0x007` | `tx.witnessesCount`                                               |
| `GTF_SCRIPT_SCRIPT`                       | `0x009` | Memory address of `tx.script`                                     |
| `GTF_SCRIPT_SCRIPT_DATA`                  | `0x00A` | Memory address of `tx.scriptData`                                 |
| `GTF_SCRIPT_INPUT_AT_INDEX`               | `0x00B` | Memory address of `tx.inputs[$rB]`                                |
| `GTF_SCRIPT_OUTPUT_AT_INDEX`              | `0x00C` | Memory address of `t.outputs[$rB]`                                |
| `GTF_SCRIPT_WITNESS_AT_INDEX`             | `0x00D` | Memory address of `tx.witnesses[$rB]`                             |
| `GTF_TX_LENGTH`                           | `0x00E` | Length of raw transaction types in memory                        |
| `GTF_CREATE_BYTECODE_WITNESS_INDEX`       | `0x101` | `tx.bytecodeWitnessIndex`                                         |
| `GTF_CREATE_STORAGE_SLOTS_COUNT`          | `0x102` | `tx.storageSlotsCount`                                            |
| `GTF_CREATE_INPUTS_COUNT`                 | `0x103` | `tx.inputsCount`                                                  |
| `GTF_CREATE_OUTPUTS_COUNT`                | `0x104` | `tx.outputsCount`                                                 |
| `GTF_CREATE_WITNESSES_COUNT`              | `0x105` | `tx.witnessesCount`                                               |
| `GTF_CREATE_SALT`                         | `0x106` | Memory address of `tx.salt`                                       |
| `GTF_CREATE_STORAGE_SLOT_AT_INDEX`        | `0x107` | Memory address of `tx.storageSlots[$rB]`                          |
| `GTF_CREATE_INPUT_AT_INDEX`               | `0x108` | Memory address of `tx.inputs[$rB]`                                |
| `GTF_CREATE_OUTPUT_AT_INDEX`              | `0x109` | Memory address of `t.outputs[$rB]`                                |
| `GTF_CREATE_WITNESS_AT_INDEX`             | `0x10A` | Memory address of `tx.witnesses[$rB]`                             |
| `GTF_INPUT_TYPE`                          | `0x200` | `tx.inputs[$rB].type`                                             |
| `GTF_INPUT_COIN_TX_ID`                    | `0x201` | Memory address of `tx.inputs[$rB].txID`                           |
| `GTF_INPUT_COIN_OUTPUT_INDEX`             | `0x202` | `tx.inputs[$rB].outputIndex`                                      |
| `GTF_INPUT_COIN_OWNER`                    | `0x203` | Memory address of `tx.inputs[$rB].owner`                          |
| `GTF_INPUT_COIN_AMOUNT`                   | `0x204` | `tx.inputs[$rB].amount`                                           |
| `GTF_INPUT_COIN_ASSET_ID`                 | `0x205` | Memory address of `tx.inputs[$rB].asset_id`                       |
| `GTF_INPUT_COIN_WITNESS_INDEX`            | `0x207` | `tx.inputs[$rB].witnessIndex`                                     |
| `GTF_INPUT_COIN_PREDICATE_LENGTH`         | `0x209` | `tx.inputs[$rB].predicateLength`                                  |
| `GTF_INPUT_COIN_PREDICATE_DATA_LENGTH`    | `0x20A` | `tx.inputs[$rB].predicateDataLength`                              |
| `GTF_INPUT_COIN_PREDICATE`                | `0x20B` | Memory address of `tx.inputs[$rB].predicate`                      |
| `GTF_INPUT_COIN_PREDICATE_DATA`           | `0x20C` | Memory address of `tx.inputs[$rB].predicateData`                  |
| `GTF_INPUT_COIN_PREDICATE_GAS_USED`       | `0x20D` | `tx.inputs[$rB].predicateGasUsed`                                 |
| `GTF_INPUT_CONTRACT_CONTRACT_ID`          | `0x225` | Memory address of `tx.inputs[$rB].contractID`                     |
| `GTF_INPUT_MESSAGE_SENDER`                | `0x240` | Memory address of `tx.inputs[$rB].sender`                         |
| `GTF_INPUT_MESSAGE_RECIPIENT`             | `0x241` | Memory address of `tx.inputs[$rB].recipient`                      |
| `GTF_INPUT_MESSAGE_AMOUNT`                | `0x242` | `tx.inputs[$rB].amount`                                           |
| `GTF_INPUT_MESSAGE_NONCE`                 | `0x243` | Memory address of `tx.inputs[$rB].nonce`                          |
| `GTF_INPUT_MESSAGE_WITNESS_INDEX`         | `0x244` | `tx.inputs[$rB].witnessIndex`                                     |
| `GTF_INPUT_MESSAGE_DATA_LENGTH`           | `0x245` | `tx.inputs[$rB].dataLength`                                       |
| `GTF_INPUT_MESSAGE_PREDICATE_LENGTH`      | `0x246` | `tx.inputs[$rB].predicateLength`                                  |
| `GTF_INPUT_MESSAGE_PREDICATE_DATA_LENGTH` | `0x247` | `tx.inputs[$rB].predicateDataLength`                              |
| `GTF_INPUT_MESSAGE_DATA`                  | `0x248` | Memory address of `tx.inputs[$rB].data`                           |
| `GTF_INPUT_MESSAGE_PREDICATE`             | `0x249` | Memory address of `tx.inputs[$rB].predicate`                      |
| `GTF_INPUT_MESSAGE_PREDICATE_DATA`        | `0x24A` | Memory address of `tx.inputs[$rB].predicateData`                  |
| `GTF_INPUT_MESSAGE_PREDICATE_GAS_USED`    | `0x24B` | `tx.inputs[$rB].predicateGasUsed`                                 |
| `GTF_OUTPUT_TYPE`                         | `0x300` | `tx.outputs[$rB].type`                                            |
| `GTF_OUTPUT_COIN_TO`                      | `0x301` | Memory address of `tx.outputs[$rB].to`                            |
| `GTF_OUTPUT_COIN_AMOUNT`                  | `0x302` | `tx.outputs[$rB].amount`                                          |
| `GTF_OUTPUT_COIN_ASSET_ID`                | `0x303` | Memory address of `tx.outputs[$rB].asset_id`                      |
| `GTF_OUTPUT_CONTRACT_INPUT_INDEX`         | `0x304` | `tx.outputs[$rB].inputIndex`                                      |
| `GTF_OUTPUT_CONTRACT_BALANCE_ROOT`        | `0x305` | Memory address of `tx.outputs[$rB].balanceRoot`                   |
| `GTF_OUTPUT_CONTRACT_STATE_ROOT`          | `0x306` | Memory address of `tx.outputs[$rB].stateRoot`                     |
| `GTF_OUTPUT_CONTRACT_CREATED_CONTRACT_ID` | `0x307` | Memory address of `tx.outputs[$rB].contractID`                    |
| `GTF_OUTPUT_CONTRACT_CREATED_STATE_ROOT`  | `0x308` | Memory address of `tx.outputs[$rB].stateRoot`                     |
| `GTF_WITNESS_DATA_LENGTH`                 | `0x400` | `tx.witnesses[$rB].dataLength`                                    |
| `GTF_WITNESS_DATA`                        | `0x401` | Memory address of `tx.witnesses[$rB].data`                        |
| `GTF_POLICY_TYPES`                        | `0x500` | `tx.policies.policyTypes`                                         |
| `GTF_POLICY_TIP`                          | `0x501` | `tx.policies[0x00].tip`                                           |
| `GTF_POLICY_WITNESS_LIMIT`                | `0x502` | `tx.policies[count_ones(0b11 & tx.policyTypes) - 1].witnessLimit` |
| `GTF_POLICY_MATURITY`                     | `0x503` | `tx.policies[count_ones(0b111 & tx.policyTypes) - 1].maturity`    |
| `GTF_POLICY_MAX_FEE`                      | `0x504` | `tx.policies[count_ones(0b1111 & tx.policyTypes) - 1].maxFee`     |
| `GTF_POLICY_EXPIRATION`                   | `0x505` | `tx.policies[count_ones(0b11111 & tx.policyTypes) - 1].expiration`|
| `GTF_UPLOAD_ROOT`                         | `0x600` | Memory address of `tx.root`                                       |
| `GTF_UPLOAD_WITNESS_INDEX`                | `0x601` | Set `$rA` to `tx.witnessIndex`                                    |
| `GTF_UPLOAD_SUBSECTION_INDEX`             | `0x602` | Set `$rA` to `tx.subsectionIndex`                                 |
| `GTF_UPLOAD_SUBSECTIONS_COUNT`            | `0x603` | Set `$rA` to `tx.subsectionsNumber`                               |
| `GTF_UPLOAD_PROOF_SET_COUNT`              | `0x604` | Set `$rA` to `tx.proofSetCount`                                   |
| `GTF_UPLOAD_PROOF_SET_AT_INDEX`           | `0x605` | Set `$rA` to `Memory address of tx.proofSet[$rB]`                 |
| `GTF_BLOB_ID`                             | `0x700` | Set `$rA` to `Memory address of tx.id`                            |
| `GTF_BLOB_WITNESS_INDEX`                  | `0x701` | Set `$rA` to the blob `tx.witnessIndex`                           |
| `GTF_UPGRADE_PURPOSE`                     | `0x800` | Set `$rA` to `Memory address of tx.purpose`                       |
| `GTF_TX_INPUTS_COUNT`                     | `0x900` | Set `$rA` to `tx.inputsCount`                                     |
| `GTF_TX_OUTPUTS_COUNT`                    | `0x901` | Set `$rA` to `tx.outputsCount`                                    |
| `GTF_TX_WITNESSES_COUNT`                  | `0x902` | Set `$rA` to `tx.witnessesCount`                                  |
| `GTF_TX_INPUT_AT_INDEX`                   | `0x903` | Set `$rA` to `Memory address of tx.inputs[$rB]`                   |
| `GTF_TX_OUTPUT_AT_INDEX`                  | `0x904` | Set `$rA` to `Memory address of t.outputs[$rB]`                   |
| `GTF_TX_WITNESS_AT_INDEX`                 | `0x905` | Set `$rA` to `Memory address of tx.witnesses[$rB]`                |

Panic if:

- `$rA` is a [reserved register](./index.md#semantics)
- `imm` is not one of the values listed above
- The value of `$rB` results in an out of bounds access for variable-length fields
- The input or output type does not match (`OutputChange` and `OutputVariable` count as `OutputCoin`)
- The requested policy type is not set for this transaction.

For fixed-length fields, the value of `$rB` is ignored.
