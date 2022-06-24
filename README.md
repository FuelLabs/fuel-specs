# Fuel Specifications

**Fuel: A Secure Decentralized Generalized Massively Scalable Transaction Ledger**

This repository specifies the Fuel protocol, including the Fuel Virtual Machine (short: FuelVM), a blazingly fast verifiable blockchain virtual machine.

- [Specification](#specification)
  - [Protocol](#protocol)
  - [FuelVM](#fuelvm)
- [Contributing](#contributing)

## Specification

### Protocol

1. [tx_format.md](./specs/protocol/tx_format.md): Fuel transaction format.
1. [identifiers.md](./specs/protocol/identifiers.md): How to compute unique IDs for transactions and UTXOs.
1. [tx_validity.md](./specs/protocol/tx_validity.md): Defines transaction validity rules.
1. [cryptographic_primitives](./specs/protocol/cryptographic_primitives.md): Cryptographic primitives used in Fuel.
1. [abi.md](./specs/protocol/abi.md): ABI specifications.
1. [storage_initialization.md](./specs/protocol/storage_initialization.md): JSON format for contract storage slot initialization.

### FuelVM

1. [main.md](./specs/vm/main.md): Describes the FuelVM at a high level, from its architecture to how it is initialized.
1. [instruction_set.md](./specs/vm/instruction_set.md): Defines the FuelVM instruction set.

## Contributing

Markdown files must conform to [GitHub Flavored Markdown](https://github.github.com/gfm/). Markdown must be formatted with:

- [markdownlint](https://github.com/DavidAnson/markdownlint)
- [Markdown Table Prettifier](https://github.com/darkriszty/MarkdownTablePrettify-VSCodeExt)
