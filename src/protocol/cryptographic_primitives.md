# Cryptographic Primitives

- [Hashing](#hashing)
- [Merkle Trees](#merkle-trees)
  - [Binary Merkle Tree](#binary-merkle-tree)
  - [Binary Merkle Sum Tree](#binary-merkle-sum-tree)
  - [Sparse Merkle Tree](#sparse-merkle-tree)
- [Public-Key Cryptography](#public-key-cryptography)

## Hashing

All hashing is done with SHA-2-256 (also known as SHA-256), defined in [FIPS 180-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf).

## Merkle Trees

Three Merkle tree structures are used: a Binary Merkle Tree (to commit to bytecode), a Binary Merkle Sum Tree (to commit to transactions and collected fees) and a Sparse Merkle Tree (to commit to contract storage, i.e. state).

### Binary Merkle Tree

A specification for the Binary Merkle Tree is [here](https://github.com/celestiaorg/celestia-specs/blob/master/src/specs/data_structures.md#binary-merkle-tree).

### Binary Merkle Sum Tree

The Binary Merkle Sum Tree is an extension of the tree defined in [RFC-6962](https://tools.ietf.org/html/rfc6962).

The root pair `(fee, digest)` of an empty tree is:

```text
(0x0000000000000000, hash()) = (0x0000000000000000, 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855)
```

The root pair of a tree with one leaf:

```text
(leaf.fee, hash(0x00 ++ leaf.fee ++ serialize(leaf)))
```

The root pair of a tree with two or more leaves is defined recursively:

```text
(left.fee + right.fee, hash(0x01 ++ left.fee ++ left.digest ++ right.fee ++ right.digest))
```

In other words, the root pair is 40 bytes (8 for fee sum, 32 for hash digest).

### Sparse Merkle Tree

A specification for the Sparse Merkle Tree is [here](https://github.com/celestiaorg/celestia-specs/blob/master/src/specs/data_structures.md#sparse-merkle-tree).

A specification describing a suite of test vectors and outputs of a Sparse Merkle Tree is [here](../tests/sparse_merkle_tree_tests.md).

## Public-Key Cryptography

Consensus-critical data is authenticated using [ECDSA](https://www.secg.org/sec1-v2.pdf), with the curve [secp256k1](https://en.bitcoin.it/wiki/Secp256k1). A highly-optimized library is available in C (<https://github.com/bitcoin-core/secp256k1>), with wrappers in Go (<https://pkg.go.dev/github.com/ethereum/go-ethereum/crypto/secp256k1>) and Rust (<https://docs.rs/crate/secp256k1>).

Public keys are encoded in uncompressed form, as the concatenation of the `x` and `y` values. No prefix is needed to distinguish between encoding schemes as this is the only encoding supported.

Deterministic signatures ([RFC-6979](https://tools.ietf.org/rfc/rfc6979.txt)) should be used when signing, but this is not enforced at the protocol level as it cannot be.

Signatures are represented as the `r` and `s` (each 32 bytes), and `v` (1-bit) values of the signature. `r` and `s` take on their usual meaning (see: [SEC 1, 4.1.3 Signing Operation](https://www.secg.org/sec1-v2.pdf)), while `v` is used for recovering the public key from a signature more quickly (see: [SEC 1, 4.1.6 Public Key Recovery Operation](https://www.secg.org/sec1-v2.pdf)). Only low-`s` values in signatures are valid (i.e. `s <= secp256k1.n//2`); `s` can be replaced with `-s mod secp256k1.n` during the signing process if it is high. Given this, the first bit of `s` will always be `0`, and can be used to store the 1-bit `v` value.

`v` represents the parity of the `Y` component of the point, `0` for even and `1` for odd. The `X` component of the point is assumed to always be low, since [the possibility of it being high is negligible](https://bitcoin.stackexchange.com/a/38909).

Putting it all together, the encoding for signatures is:

<!-- markdownlint-disable-next-line MD040 -->
```
|    32 bytes   ||           32 bytes           |
[256-bit r value][1-bit v value][255-bit s value]
```

This encoding scheme is derived from [EIP 2098: Compact Signature Representation](https://eips.ethereum.org/EIPS/eip-2098).
