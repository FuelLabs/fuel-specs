# Fuel VM Specification

This document gives the Fuel VM specification. 

The specification covers the types, opcodes, and execution semantics.

## Table of Contents

* [Semantics](#semantics)
* [Opcodes](#opcodes)

## Semantics

Fuel instructions are 64 bits wide with the following two types:

1. Arithmetic/Logic Unit-based (ALU) opcodes:
   * Opcode: 8 bits, start with 0 in MSB
   * Register identifier: 6 bits 
   * Immediate values: 32 bits
1. Ethereum-related opcodes:
   * Opcode: 8 bits, start with 1 in MSB
   * Register identifier: 6 bits 
   * Immediate values: no Ethereum-related opcodes use immediate values

In addition, there are some special registers defined as follows:

* $z: zero-containing register (convenience register frequently found in register machines)
* $hi: register containing high bits of multiplication/division result; remainder in div
* $lo: register containing low bits of multiplication/division result; divisor in div
* $of: bit-sized register indicating overflow of signed operation
* $uf: bit-sized register indicating underflow of signed operation
* $state: 2-bit sized register indicating statemachine status: RUNNING, HALTED

Default Values:

* VM_MAX_RAM: 32MB

We assume that a Fuel VM can start if it has the capability of (potentially dynamically) allocating up to VM_MAX_RAM of main memory; we also depend on a simple assumption that a Fuel VM does not deallocate memory. 
Allocations/reading/writing of memory take place by instructing the underlying operating system, implemented by the Fuel VM smart contract compiler and interpreter.

A register machine executes on commodity hardware by reading sequences of bytecodes that represent instructions and corresponding input into an instruction register. 

A Fuel VM interpreter reads the instruction register to first determine the opcode, by which to then interpret the input to the opcode.

Fuel VM opcodes belong to one of two types: ALU opcodes, which represent arithmetic and logic operations, and Ethereum opcodes, which represent blockchain-related opcodes.

ALU opcodes have operands that use 6-bit register identifiers, permitting access to up to 64 in-memory registers, and 32-bit wide immediate values.
Ethereum opcodes have operands that use 6-bit register identifiers, permitting access to up to 64 in-memory registers (and _no_ immediate values).

By default, registers reference 64-bit wide values stored in main-memory, unless explicitly defined to alternative widths, such as to improve performance of cryptography operations. 

The Fuel VM does not serialize/deserialize data to/from disk, only main-memory, thus the specification does not define an explicit storage model.



Ethereum-related registers, e.g., gas, are defined in the execution context using memory-mapped variables. The implication is that a smart contract executing in a Fuel VM can write to less than 32MB of memory.


## Opcodes

This section describes the Fuel VM's opcodes. 

Fuel VM opcodes derive from the MIPS instruction set, and from the [Ethereum Yellow Paper](https://github.com/ethereum/yellowpaper).

Additionally, the Fuel VM defines opcodes in support of optimistic rollups and specialized performance optimizations.

A complete list of opcodes in the Fuel VM is [documented](opcodes.md).


<!--
## Implementation

This section describes the implementation of the opcodes. 

An Ethereum 1x on-chain implementation of a Fuel VM interpreter will be available [here](https://github.com/FuelLabs/fuel-vm-evm).

### Optimizations

This section defines optimizations that are applicable to the Fuel VM.


#### On-chain optimizations

In this section, we describe on-chain optimizations.

Potential topics:

* smart contract patterns
* compression
* passthrough
* client implementations

#### Off-chain optimizations

In this section, we describe the off-chain optimizations. 

Potential topics: 
* compression
* indexing
* parallelism

-->