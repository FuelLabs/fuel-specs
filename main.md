# Fuel VM Specification

- [Introduction](#introduction)
- [Parameters](#parameters)
- [Semantics](#semantics)
- [Opcodes](#opcodes)
- [Transaction Execution](#transaction-execution)
- [Call Frames](#call-frames)

## Introduction

This document provides the specification for the Fuel Virtual Machine (FuelVM). The specification covers the types, opcodes, and execution semantics.

## Parameters

| name         | type     | value   | note   |
| ------------ | -------- | ------- | ------ |
| `VM_MAX_RAM` | `uint64` | `2**20` | 1 MiB. |

## Semantics

Fuel instructions are 32 bits wide (4 bytes) and comprise of:
* Opcode: 8 bits
* Register identifier: 6 bits
* Immediate value: 12 or 24 bits, depending on operation

In addition, there are some special registers defined as follows:
| register | name            | description                                                                                                            |
| -------- | --------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `$z`     | zero            | Zero-containing register (convenience register frequently found in register machines).                                 |
| `$of`    | overflow        | Register containing high bits of multiplication, remainder in division, or overflow of signed addition or subtraction. |
| `$pc`    | program counter | The program counter.                                                                                                   |
| `$fp`    | frame pointer   | Memory address of beginning of current call frame.                                                                     |
| `$fpp`   | prev frame ptr  | Previous call frame's frame pointer.                                                                                   |
| `$hp`    | heap pointer    | Memory address of the current bottom of the heap.                                                                      |
| `$hpp`   | prev heap ptr   | Previous call frame's heap pointer.                                                                                    |
| `$err`   | error           | Error codes for particular operations.                                                                                 |
| `$gas`   | gas             | Remaining gas.                                                                                                         |

Integers are represented in [big-endian](https://en.wikipedia.org/wiki/Endianness) format, and all operations are unsigned. Boolean `false` is `0` and Boolean `true` is `1`.

Registers are 64 bits (8 bytes) wide. Words are the same width as registers.

Persistent state (i.e. storage) is a key-value store with 32-byte keys and 32-byte values. Each contract has its own persistent state that is independent of other contracts. This is committed to in a Sparse Binary Merkle Tree.

## Opcodes

A complete list of opcodes in the Fuel VM is documented [here](./opcodes.md).

## Transaction Execution

If script bytecode is present, transaction validation requires execution.

A single monolithic memory of size `VM_MAX_RAM` bytes is allocated, indexed by individual byte. A stack and heap memory model is used, allowing for dynamic memory allocation. The stack begins at `0` and grows upward. The heap begins at `VM_MAX_RAM-1` and grows downward.

Before execution begins, the following is pushed on the stack sequentially:
1. Block number (word-aligned).
1. Block producer address (word-aligned).
1. Block gas limit (word-aligned).
1. Transaction hash (word-aligned).
1. Block hash for the previous 256 blocks (word-aligned).
1. The [transaction](./tx_format.md).

`$pc` is initialized to the start of the transaction's script bytecode and execution begins.

## Call Frames

