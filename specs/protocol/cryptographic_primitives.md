# Cryptographic Primitives

- [Hashing](#hashing)
- [Merkle Trees](#merkle-trees)
    - [Binary Merkle Tree](#binary-merkle-tree)
    - [Binary Merkle Sum Tree](#binary-merkle-sum-tree)
    - [Sparse Merkle Tree](#sparse-merkle-tree)

## Hashing

All hashing is done with SHA-2-256 (also known as SHA-256), defined in [FIPS 180-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf).

## Merkle Trees

Three Merkle tree structures are used: a Binary Merkle Sum Tree (to commit to bytecode), a Binary Merkle Sum Tree (to commit to transactions and collected fees) and a Sparse Merkle Tree (to commit to contract storage, i.e. state).

### Binary Merkle Tree

A specification for the Sparse Merkle Tree is [here](https://github.com/lazyledger/lazyledger-specs/blob/master/specs/data_structures.md#binary-merkle-tree).

### Binary Merkle Sum Tree

The Binary Merkle Sum Tree is an extension of the tree defined in [RFC-6962](https://tools.ietf.org/html/rfc6962).

The root pair `(fee, digest)` of an empty tree is:
```
(0x0000000000000000, hash()) = (0x0000000000000000, 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855)
```

The root pair of a tree with one leaf:
```
(leaf.fee, hash(0x00 ++ serialize(leaf)))
```

The root pair of a tree with two or more leaves is defined recursively:
```
(left.fee + right.fee, hash(0x01 ++ left.fee ++ left.digest ++ right.fee ++ right.digest))
```

In other words, the root pair is 40 bytes (8 for fee sum, 32 for hash digest).

### Sparse Merkle Tree

A specification for the Sparse Merkle Tree is [here](https://github.com/lazyledger/lazyledger-specs/blob/master/specs/data_structures.md#sparse-merkle-tree).
