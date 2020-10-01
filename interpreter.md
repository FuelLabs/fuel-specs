# The Fuel VM Interpreter

This document will provide details on the Fuel VM interpreter environment. 

Details on the interpreter opcodes and execution semantics is provided in the [main document](main.md).

## Features 

We aim to provide a custom Fuel VM environment with the following features:

* opcode extensibility and modularity, such as for rollups and cryptography
* modular and optimized algorithms not considered/implemented in public and enterprise Ethereum standards
* support for diverse and improved back-end data structures
* novel and extensible smart contract mechanisms

The Fuel VM's use of the above VM features enables support for the following technical requirements:
* [MAST (Merkleized Alternative Script Tree)](https://bitcoinops.org/en/topics/mast/)
* Ethereum smart contracts
* [UTXO data model](https://en.bitcoin.it/wiki/Transaction)
* [Covenants](https://fc16.ifca.ai/bitcoin/papers/MES16.pdf)

Essential reading:

[BIP 116: MERKLEBRANCHVERIFY](https://github.com/bitcoin/bips/blob/master/bip-0116.mediawiki#motivation)

[Accounts, Strict Access Lists, and UTXOs](https://hackmd.io/KOJdKANHSvaGC_8IugEAJA#)

[Segregated Witness](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki)

[Fast Merkle Trees](https://github.com/bitcoin/bips/blob/master/bip-0098.mediawiki); this reference's description of Merkle tree inclusion proofs is relevant.

[BIP 117: Tail Call Execution Semantics](https://github.com/bitcoin/bips/blob/master/bip-0117.mediawiki)





## Techniques 

The specific approach we are currently targeting are:

* compression of witnesses via BLS
* compression of account sets via registrations maps and batching
* flexible and hierarchical use of roots and commitments

## Ethereum Compatibility

A Fuel VM is initially intended to run within an Ethereum-like environment, that is with smart contract capabilities. 

The Fuel VM will provide an execution context in which Ethereum's VM semantics will be supported. 

In order to achieve The Fuel interpreter will also have a mode to support a "superset" of Ethereum's functionality.

## Implementation

To support our desired feature list, there are several approaches to consider (some may be used in conjunction depending on operational needs):

1. A Fuel VM, which can be written as an interpreter in Solidity, for execution on any existing Ethereum-capable blockchain client.

1. A Fuel VM, based on an existing modular Ethereum blockchain client, with custom Fuel VM opcodes implementations in the client's native programming language, e.g., Java, Python, Rust, Go.

1. Modified Solidity compiler which is used to recompile existing Solidity smart contracts (without any changes). The recompilation will generate Fuel VM bytecode that can execute in a Fuel-compatible VM or interpreter. 

## Related Work

SK- Remove the following
### [Arbitrum](https://github.com/OffchainLabs/arbitrum)

This is a tree-based, staked optimistic rollup with a dispute period and movable stakes down a tree. Such capabilities enable for different levels of trust, from staker-based 1-confirmation transactions, consensus-based transaction confirmations, and Layer 1-based transaction confirmations.  

### [Optimism](https://github.com/ethereum-optimism/optimism-monorepo)

The Optimism Medium [article](https://medium.com/plasma-group/ethereum-smart-contracts-in-l2-optimistic-rollup-2c1cef2ec537) article shows a figure which seems to show that tail call semantics are not supported.  

An interesting [article](https://medium.com/plasma-group/introducing-the-ovm-db253287af50) about the Optimism Virtual Machine (OVM); as well, there are some mathematical forms specified.
 
 
### Scalable Smart Contracts

Nick Mudge, "EIP-2535: Diamond Standard [DRAFT]," Ethereum Improvement Proposals, no. 2535, February 2020. [Online serial]. Available: https://eips.ethereum.org/EIPS/eip-2535.

---

### Virtual Machines

Yunhe Shi, Kevin Casey, M. Anton Ertl, and David Gregg. 2008. Virtual machine showdown: Stack versus registers. ACM Trans. Archit. Code Optim. 4, 4, Article 2 (January 2008), 36 pages. Available via [Usenix](https://www.usenix.org/events%2Fvee05%2Ffull_papers/p153-yunhe.pdf). DOI: https://doi.org/10.1145/1328195.1328197.

### Compact Fraud Proofs

John Adler, [Compact Fraud Proofs for UTXO Chains Without Intermediate State Serialization](https://ethresear.ch/t/compact-fraud-proofs-for-utxo-chains-without-intermediate-state-serialization/5885)