# Fuel VM Specification

This document gives the Fuel VM specification. 

The specification covers the types, opcodes, and execution semantics.

## Table of Contents

* Types
* Opcodes
* Format
* Implementation
* Optimizations
* Todo

## Types

At this iteration, we only consider a 256-bit type, i.e., we do not distinguish between bytes and (signed/unsigned) integers. This may change as the specification evolves.

## Opcodes

This section describes the Fuel VM's opcodes. 

Many opcodes are derived from the EVM opcodes specified in the [Ethereum Yellow Paper](https://github.com/ethereum/yellowpaper).

Additional opcodes are defined for the support of optimistic rollups and performance optimizations.

A complete list of opcodes in the Fuel VM are [documented](opcodes.md).

## Format 

The instruction format is defined as follows:

* opcode: 8 bits
* destination register: 4 bits
* source register: 4 bits
* source register: 4 bits
* immediate values: 16 bits

In addition, there are some special registers defined as follows:

* $z: zero-containing register
* $hi: register containing high bits of multiplication/division result; remainder in div
* $lo: register containing low bits of multiplication/division result; divisor in div
* $of: bit-sized register indicating overflow
* $uf: bit-sized register indicating underflow
* $state: 2-bit sized register indicating statemachine status: RUNNING, HALTED


* Introspect part of the transaction - new opcodes
* A different memory model from Ethereum potentially
* Change to register machine

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


## Todo

* Add link to the yellowpaper
