# Fuel Specifications

<!-- markdownlint-disable-next-line MD036 -->
**Fuel: A Secure Decentralized Generalized Massively Scalable Transaction Ledger**

This book specifies the Fuel protocol, including the Fuel Virtual Machine
(short: FuelVM), a blazingly fast verifiable blockchain virtual machine.

## Protocol

- [**Transaction Format**](./protocol/tx_format/index.md) - The Fuel transaction format.
- [**Computing Identifiers**](./protocol/id/index.md) - Computing unique IDs for transactions, contracts and UTXOs.
- [**Transaction Validity**](./protocol/tx_validity.md) - Defines transaction validity rules.
- [**Cryptographic Primitives**](./protocol/cryptographic_primitives.md) - Cryptographic primitives used in Fuel.
- [**Application Binary Interface (ABI)**](./protocol/abi.md) - Low-level details on interfacing with Fuel bytecode.
- [**Storage Slot Initialization**](./protocol/storage_initialization.md) - JSON format for contract storage slot initialization.
- [**Block Header Format**](./protocol/block_header.md) - The Fuel block header format.

## FuelVM

- [**Overview**](./vm/index.md) - Describes the FuelVM at a high level, from its architecture to how it is initialized.
- [**Instruction Set**](./vm/instruction_set.md) - Defines the FuelVM instruction set.

## Network-Specific

- [**Proof of Authority (PoA)**](./network/poa.md) - The Fuel Proof of Authority Network.

## Testing

- [**Sparse Merkle Tree**](./tests/sparse_merkle_tree_tests.md) - A test suite for verifying correctness of SMT outputs.
