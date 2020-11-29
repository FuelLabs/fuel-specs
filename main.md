# Fuel VM Specification

- [Introduction](#introduction)
- [Parameters](#parameters)
- [Semantics](#semantics)
- [Opcodes](#opcodes)
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
| `$err`   | error           | Error codes for particular operations.                                                                                 |
| `$gas`   | gas             | Remaining gas.                                                                                                         |

We assume that a FuelVM can start if it has the capability of (potentially dynamically) allocating up to `VM_MAX_RAM` of main memory; we also depend on a simple assumption that a Fuel VM does not deallocate memory.

Integers are represented in [big-endian](https://en.wikipedia.org/wiki/Endianness) format, and all operations are unsigned. Boolean `false` is `0` and Boolean `true` is `1`.

Registers are 64 bits (8 bytes) wide. Words are the same width as registers.

The FuelVM does not serialize/deserialize data to/from disk, only main memory, thus the specification does not define an explicit persistent storage implementation.

## Opcodes

A complete list of opcodes in the Fuel VM is documented [here](./opcodes.md).

## Call Frames

