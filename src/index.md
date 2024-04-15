# Fuel Specifications

<!-- markdownlint-disable-next-line MD036 -->
**Fuel: A Secure Decentralized Generalized Massively Scalable Transaction Ledger**

This book specifies the Fuel protocol, including the Fuel Virtual Machine
(short: FuelVM), a blazingly fast verifiable blockchain virtual machine.

## Protocol

- [**Transaction Format**](./tx-format/index.md) - The Fuel transaction format.
- [**Computing Identifiers**](./identifiers/index.md) - Computing unique IDs for transactions, contracts and UTXOs.
- [**Transaction Validity**](./protocol/tx-validity.md) - Defines transaction validity rules.
- [**Cryptographic Primitives**](./protocol/cryptographic-primitives.md) - Cryptographic primitives used in Fuel.
- [**Application Binary Interface (ABI)**](./abi/index.md) - Low-level details on interfacing with Fuel bytecode.
- [**Storage Slot Initialization**](./protocol/storage-initialization.md) - JSON format for contract storage slot initialization.
- [**Block Header Format**](./protocol/block-header.md) - The Fuel block header format.
- [**Relayer/Bridge**](./protocol/relayer.md) - The Fuel relayer/bridge protocol.

## FuelVM

- [**Overview**](./fuel-vm/index.md) - Describes the FuelVM at a high level, from its architecture to how it is initialized.
- [**Instruction Set**](./fuel-vm/instruction-set.md) - Defines the FuelVM instruction set.

## Network-Specific

- [**Proof of Authority (PoA)**](./networks/poa.md) - The Fuel Proof of Authority Network.

## Testing

- [**Sparse Merkle Tree**](./tests/sparse-merkle-tree-tests.md) - A test suite for verifying correctness of SMT outputs.
