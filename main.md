# Fuel VM Specification

- [Introduction](#introduction)
- [Parameters](#parameters)
- [Semantics](#semantics)
- [Opcodes](#opcodes)

## Introduction

This document provides the specification for the Fuel Virtual Machine (FuelVM). The specification covers the types, opcodes, and execution semantics.

## Parameters

| name         | type     | value   | note    |
| ------------ | -------- | ------- | ------- |
| `VM_MAX_RAM` | `uint64` | `2**25` | 32 MiB. |

## Semantics

Fuel instructions are 32 bits wide (4 bytes) and comprise of:
* Opcode: 8 bits
* Register identifier: 6 bits
* Immediate value: 12 bits

In addition, there are some special registers defined as follows:
| register | name            | description                                                                                                            |
| -------- | --------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `$z`     | zero            | Zero-containing register (convenience register frequently found in register machines).                                 |
| `$of`    | overflow        | Register containing high bits of multiplication, remainder in division, or overflow of signed addition or subtraction. |
| `$pc`    | program counter | The program counter.                                                                                                   |
| `$err`   | error           | Error codes for particular operations.                                                                                 |
| `$gas`   | gas             | Remaining gas.                                                                                                         |

We assume that a FuelVM can start if it has the capability of (potentially dynamically) allocating up to `VM_MAX_RAM` of main memory; we also depend on a simple assumption that a Fuel VM does not deallocate memory.

Arithmetic opcodes have operands that use 6-bit register identifiers, permitting access to up to 64 registers, and 12-bit immediate values. Ethereum-style opcodes have operands that use 6-bit register identifiers, permitting access to up to 64 in-memory registers (and no immediate values).

Integers are represented in [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) [big-endian](https://en.wikipedia.org/wiki/Endianness) format.

Registers are 64 bits (8 bytes) wide.

The FuelVM does not serialize/deserialize data to/from disk, only main memory, thus the specification does not define an explicit persistent storage implementation.

## Opcodes

A complete list of opcodes in the Fuel VM is documented [here](./opcodes.md).
