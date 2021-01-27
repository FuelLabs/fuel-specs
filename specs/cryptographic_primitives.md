# Cryptographic Primitives

## Hashing

All hashing is done with SHA-2-256 (also known as SHA-256), defined in [FIPS 180-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf).

## Merkle Trees

Two Merkle tree structures are used: a plain Binary Merkle Tree (to commit to transactions) and a Sparse Merkle Tree (to commit to contract storage, i.e. state). A specification for the Binary Merkle Tree is [here](https://github.com/lazyledger/lazyledger-specs/blob/master/specs/data_structures.md#binary-merkle-tree) and a specification for the Sparse Merkle Tree is [here](https://github.com/lazyledger/lazyledger-specs/blob/master/specs/data_structures.md#sparse-merkle-tree).
