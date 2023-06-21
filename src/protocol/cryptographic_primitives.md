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

The Binary Merkle Sum Tree is an extension of the tree defined in [RFC-6962](https://www.rfc-editor.org/rfc/rfc9162).

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

Before insertion of the key-value pair, each key of the Sparse Merkle Tree should be hashed with `sha256` to prevent tree structure manipulations.
During the proof verification, the original leaf key should be hashed similarly. Otherwise, the root will not match.

## ECDSA Public-Key Cryptography

Consensus-critical data is authenticated using [ECDSA](https://www.secg.org/sec1-v2.pdf), with the curve [secp256k1](https://en.bitcoin.it/wiki/Secp256k1). A highly-optimized library is available in C (<https://github.com/bitcoin-core/secp256k1>), with wrappers in Go (<https://pkg.go.dev/github.com/ethereum/go-ethereum/crypto/secp256k1>) and Rust (<https://docs.rs/crate/secp256k1>).

Public keys are encoded in uncompressed form, as the concatenation of the `x` and `y` values. No prefix is needed to distinguish between encoding schemes as this is the only encoding supported.

Deterministic signatures ([RFC-6979](https://www.rfc-editor.org/rfc/rfc6979)) should be used when signing, but this is not enforced at the protocol level as it cannot be.

Signatures are represented as the `r` and `s` (each 32 bytes), and `v` (1-bit) values of the signature. `r` and `s` take on their usual meaning (see: [SEC 1, 4.1.3 Signing Operation](https://www.secg.org/sec1-v2.pdf)), while `v` is used for recovering the public key from a signature more quickly (see: [SEC 1, 4.1.6 Public Key Recovery Operation](https://www.secg.org/sec1-v2.pdf)). Only low-`s` values in signatures are valid (i.e. `s <= secp256k1.n//2`); `s` can be replaced with `-s mod secp256k1.n` during the signing process if it is high. Given this, the first bit of `s` will always be `0`, and can be used to store the 1-bit `v` value.

`v` represents the parity of the `Y` component of the point, `0` for even and `1` for odd. The `X` component of the point is assumed to always be low, since [the possibility of it being high is negligible](https://bitcoin.stackexchange.com/a/38909).

Putting it all together, the encoding for signatures is:

<!-- markdownlint-disable-next-line MD040 -->
```
|    32 bytes   ||           32 bytes           |
[256-bit r value][1-bit v value][255-bit s value]
```

This encoding scheme is derived from [EIP 2098: Compact Signature Representation](https://eips.ethereum.org/EIPS/eip-2098).

## EdDSA Public-Key Cryptography

[Ed25519](https://datatracker.ietf.org/doc/html/rfc8032) is supported for use by applications built on Fuel. Edwards curve operations are performed by the [ed25519-dalek](https://github.com/dalek-cryptography/ed25519-dalek) Rust library.

Public keys are encoded in compressed form as specified by the Ed25519 format [RFC-8032 5.1.5](https://datatracker.ietf.org/doc/html/rfc8032#section-5.1.5). Point compression is performed by replacing the most significant bit in the final octet of the `y` coordinate with the sign bit from the `x` coordinate:

```rust
let mut pk = y;
pk ^= x.is_negative().unwrap_u8() << 7;
```

Public keys are required to be strong enough to prevent malleability, and are checked for weakness during signature verification.

Signatures are 64 bytes, represented as the concatenation of `R` (32 bytes) and `S` (32 bytes) Where `R` and `S` are defined in [RFC-8032 5.1.6](https://datatracker.ietf.org/doc/html/rfc8032#section-5.1.6).

Signatures must conform to strict verification requirements to avoid malleability concerns. While this is not part of the original Ed25519 specification, it has become a growing concern especially when used in cryptocurrency applications.

The two areas of malleability concern verified by the dalek library are:

1. Scalar Malleability
  The authors of the RFC explicitly stated that verification of an ed25519 signature must fail if the scalar s is not properly reduced mod $\ell$:
  To verify a signature on a message M using public key A, with F being 0 for Ed25519ctx, 1 for Ed25519ph, and if Ed25519ctx or Ed25519ph is being used, C being the context, first split the signature into two 32-octet halves. Decode the first half as a point R, and the second half as an integer S, in the range 0 <= s < L. Decode the public key A as point A'. If any of the decodings fail (including S being out of range), the signature is invalid.)

2. Point Malleability
  The authors of the RFC added in a malleability check to step #3 in §5.1.7, for small torsion components in the R value of the signature, which is not strictly required, as they state:

  > Check the group equation [8][S]B = [8]R + [8][k]A'. It's sufficient, but not required, to instead check [S]B = R + [k]A'.

The risks of EdDSA malleability are explained in further detail by Harry's [blogpost](https://hdevalence.ca/blog/2020-10-04-its-25519am).
