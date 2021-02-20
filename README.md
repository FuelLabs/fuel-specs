# Fuel Specifications

This repository specifies the Fuel v2 system, including the Fuel Virtual Machine (FuelVM, FVM), a next-generation verifiable virtual machine for [the Fuel v2 blockchain](https://github.com/FuelLabs).

- [Specification](#specification)
    - [Protocol](#protocol)
    - [FuelVM](#fuelvm)

## Specification

### Protocol

1. [tx_validity.md](./specs/protocol/tx_validity.md): Defines transaction validity rules.
1. [tx_format.md](./specs/protocol/tx_format.md): Transaction format.

### FuelVM
The specification is laid out as follows:
1. [main.md](./specs/vm/main.md): Entry point for reading the specification. Describes the FuelVM at a high level, from its architecture to how it is initialized.
1. [opcodes.md](./specs/vm/opcodes.md): Defines all opcodes available in the FuelVM.
