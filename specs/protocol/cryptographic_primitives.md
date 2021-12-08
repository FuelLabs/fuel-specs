# Cryptographic Primitives

- [Hashing](#hashing)
- [Merkle Trees](#merkle-trees)
  - [Binary Merkle Tree](#binary-merkle-tree)
  - [Binary Merkle Sum Tree](#binary-merkle-sum-tree)
  - [Sparse Merkle Tree](#sparse-merkle-tree)

## Hashing

All hashing is done with SHA-2-256 (also known as SHA-256), defined in [FIPS 180-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf).

## Merkle Trees

Three Merkle tree structures are used: a Binary Merkle Tree (to commit to bytecode), a Binary Merkle Sum Tree (to commit to transactions and collected fees) and a Sparse Merkle Tree (to commit to contract storage, i.e. state).

### Binary Merkle Tree

A specification for the Binary Merkle Tree is [here](https://github.com/celestiaorg/celestia-specs/blob/master/src/specs/data_structures.md#binary-merkle-tree).

### Binary Merkle Sum Tree

The Binary Merkle Sum Tree is an extension of the tree defined in [RFC-6962](https://tools.ietf.org/html/rfc6962).

Nodes contain four fields:

| name        | type   | description                                   |
|-------------|--------|-----------------------------------------------|
| `fee`       | uint64 | Node fee                                      |
| `digest`    | Hash   | Node value                                    |
| `left_key`  | Hash   | Value of the left child (for internal nodes)  |
| `right_key` | Hash   | Value of the right child (for internal nodes) |

For leaf node `node` with fee `fee` and data `data`:
```
node.digest = hash(0x00 ++ fee ++ data)
```

For internal node `node` with left child `left` and right child `right`:
```
node.digest = hash(0x01 ++ left.fee ++ left.digest ++ right.fee ++ right.digest)
```

#### Root Pairs

The root pair `(fee, digest)` of an empty tree is:

```
(0x0000000000000000, hash()) = (0x0000000000000000, 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855)
```

The root pair of a tree with one leaf:

```
(leaf.fee, hash(0x00 ++ leaf.fee ++ serialize(leaf)))
```

The root pair of a tree with two or more leaves is defined recursively:

```
left = get(node.left_key)
right = get(node.right_key)
(left.fee + right.fee, hash(0x01 ++ left.fee ++ left.digest ++ right.fee ++ right.digest))
```

In other words, the root pair is 40 bytes (8 for fee sum, 32 for hash digest).

### Sparse Merkle Tree

A specification for the Sparse Merkle Tree is [here](https://github.com/celestiaorg/celestia-specs/blob/master/src/specs/data_structures.md#sparse-merkle-tree).
