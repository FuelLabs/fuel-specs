# Cryptographic Primitives

- [Cryptographic Primitives](#cryptographic-primitives)
  - [Hashing](#hashing)
  - [`HashDigest`](#hashdigest)
  - [Merkle Trees](#merkle-trees)
    - [Binary Merkle Tree](#binary-merkle-tree)
      - [Binary Merkle Tree Inclusion Proofs](#binary-merkle-tree-inclusion-proofs)
    - [Sparse Merkle Tree](#sparse-merkle-tree)
      - [Insertion](#insertion)
      - [Sparse Merkle Tree Inclusion Proofs](#sparse-merkle-tree-inclusion-proofs)
  - [ECDSA Public-Key Cryptography](#ecdsa-public-key-cryptography)
  - [EdDSA Public-Key Cryptography](#eddsa-public-key-cryptography)

## Hashing

All hashing is done with SHA-2-256 (also known as SHA-256), defined in [FIPS 180-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf).

## `HashDigest`

Output of the [hashing](#hashing) function. Exactly 256 bits (32 bytes) long.

## Merkle Trees

Two Merkle tree structures are used: a Binary Merkle Tree (to commit to bytecode) and a Sparse Merkle Tree (to commit to contract storage, i.e. state).

### Binary Merkle Tree

Binary Merkle trees are constructed in the same fashion as described in [Certificate Transparency (RFC-6962)](https://tools.ietf.org/html/rfc6962), except for using [a different hashing function](#hashing). Leaves are hashed once to get leaf node values and internal node values are the hash of the concatenation of their children (either leaf nodes or other internal nodes).

Nodes contain a single field:

| name | type                      | description |
|------|---------------------------|-------------|
| `v`  | [`HashDigest`](#hashdigest) | Node value. |

The base case (an empty tree) is defined as the [hash](#hashing) of the empty string:

```C++
node.v = 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
```

For leaf node `node` of leaf data `d`:

```C++
node.v = h(0x00, serialize(d))
```

For internal node `node` with children `l` and `r`:

```C++
node.v = h(0x01, l.v, r.v)
```

Note that rather than duplicating the last node if there are an odd number of nodes (the [Bitcoin design](https://github.com/bitcoin/bitcoin/blob/5961b23898ee7c0af2626c46d5d70e80136578d3/src/consensus/merkle.cpp#L9-L43)), trees are allowed to be imbalanced. In other words, the height of each leaf may be different. For an example, see Section 2.1.3 of [Certificate Transparency (RFC-6962)](https://tools.ietf.org/html/rfc6962#section-2.1.3).

Leaves and internal nodes are hashed differently: the one-byte `0x00` is prepended for leaf nodes while `0x01` is prepended for internal nodes. This avoids a second-preimage attack [where internal nodes are presented as leaves](https://en.wikipedia.org/wiki/Merkle_tree#Second_preimage_attack) trees with leaves at different heights.

#### Binary Merkle Tree Inclusion Proofs

| name       | type                          | description                                                     |
|------------|-------------------------------|-----------------------------------------------------------------|
| `root`     | [`HashDigest`](#hashdigest)`[]` | The expected root of the Merkle tree.                           |
| `data`     | Bytes                         | The data of the leaf (unhashed).                                |
| `siblings` | [`HashDigest`](#hashdigest)`[]` | Sibling hash values, ordered starting from the leaf's neighbor. |

A proof for a leaf in a [binary Merkle tree](#binary-merkle-tree), as per Section 2.1.1 of [Certificate Transparency (RFC-6962)](https://tools.ietf.org/html/rfc6962#section-2.1.1).

In some contexts, the array of sibling hashes is also known as the proof set. Note that proof format prescribes that leaf data be in its original, unhashed state, while the proof set (array of sibling data) uses hashed data. This format precludes the proof set from itself including the leaf data from the leaf undergoing the proof; rather, proof verification explicitly requires hashing the leaf data during the calculation of the proof set root.

### Sparse Merkle Tree

Sparse Merkle Trees (SMTs) are _sparse_, i.e. they contain mostly empty leaves. They can be used as key-value stores for arbitrary data, as each leaf is keyed by its index in the tree. Storage efficiency is achieved through clever use of implicit defaults, avoiding the need to store empty leaves.

Additional rules are added on top of plain [binary Merkle trees](#binary-merkle-tree):

1. Default values are given to leaf nodes with empty leaves.
1. While the above rule is sufficient to pre-compute the values of intermediate nodes that are roots of empty subtrees, a further simplification is to extend this default value to all nodes that are roots of empty subtrees. The 32-byte zero, i.e. `0x0000000000000000000000000000000000000000000000000000000000000000`, is used as the default value. This rule takes precedence over the above one.
1. The number of hashing operations can be reduced to be logarithmic in the number of non-empty leaves on average, assuming a uniform distribution of non-empty leaf keys. An internal node that is the root of a subtree that contains exactly one non-empty leaf is replaced by that leaf's leaf node.

Nodes contain a single field:

| name | type                      | description |
|------|---------------------------|-------------|
| `v`  | [`HashDigest`](#hashdigest) | Node value. |

In the base case, where a sparse Merkle tree has `height = 0`, the root of a tree is defined as the [hash](#hashing) of the empty string:

```C++
node.v = 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
```

When a sparse Merkle tree has a height of 0, it can have no leaves, and, therefore, no default value children. The root is then calculated as the hash of the empty string, similar to that of an empty binary Merkle tree.

For a tree with `height > 0`, the root of an empty tree is defined as the default value:

```C++
node.v = 0x0000000000000000000000000000000000000000000000000000000000000000
```

Note that this is in contrast to the base case of the sparse and binary Merkle trees, where the root is the hash of the empty string. When a sparse Merkle tree has a height greater than 0, a new tree instance is composed of default value leaves. Nodes containing only default value children have the default value as well. Applying these rules recursively percolates the default value up to the tree's root.

For leaf node `node` of leaf data `d` with key `k`:

```C++
node.v = h(0x00, k, h(serialize(d)))
```

The key of leaf nodes must be prepended, since the index of a leaf node that is not at maximum depth cannot be determined without this information. Leaf values are hashed so that they do not need to be included in full in non-membership proofs.

For internal node `node` with children `l` and `r`:

```C++
node.v = h(0x01, l.v, r.v)
```

#### Insertion

Before insertion of the key-value pair, each key of the Sparse Merkle Tree should be hashed with `sha256` to prevent tree structure manipulations.
During the proof verification, the original leaf key should be hashed similarly. Otherwise, the root will not match.

#### Sparse Merkle Tree Inclusion Proofs

SMTs can further be extended with _compact_ proofs. Merkle proofs are composed, among other things, of a list of sibling node values. We note that, since nodes that are roots of empty subtrees have known values (the default value), these values do not need to be provided explicitly; it is sufficient to simply identify which siblings in the Merkle branch are roots of empty subtrees, which can be done with one bit per sibling.

For a Merkle branch of height `h`, an `h`-bit value is appended to the proof. The lowest bit corresponds to the sibling of the leaf node, and each higher bit corresponds to the next parent. A value of `1` indicates that the next value in the list of values provided explicitly in the proof should be used, and a value of `0` indicates that the default value should be used.

A proof into an SMT is structured as:

| name               | type                          | description                                                              |
|--------------------|-------------------------------|--------------------------------------------------------------------------|
| `depth`            | `uint16`                      | Depth of the leaf node. The root node is at depth `0`. Must be `<= 256`. |
| `siblings`         | [`HashDigest`](#hashdigest)`[]` | Sibling hash values, ordered starting from the leaf's neighbor.          |
| `includedSiblings` | `byte[32]`                    | Bitfield of explicitly included sibling hashes.                          |

The `includedSiblings` is ordered by most-significant-byte first, with each byte ordered by most-significant-bit first. The lowest bit corresponds to the leaf node level.

A specification describing a suite of test vectors and outputs of a Sparse Merkle Tree is [here](../tests/sparse-merkle-tree-tests.md).

## ECDSA Public-Key Cryptography
- ECDSA Public-Key Cryptography is used to authenticate consensus-critical data.
- The curve secp256k1 is utilized for this purpose.
- A highly-optimized C library with wrappers in Go and Rust is available for ECDSA operations.
- Public keys are encoded in uncompressed form, consisting of concatenated x and y values.
- Deterministic signatures (RFC-6979) are recommended but not enforced at the protocol level.
- Signatures consist of r and s values, with v representing the parity of the Y component of the point.
- Only low-s values in signatures are valid, and the first bit of s is used to store the 1-bit v value.
- The X component of the point is assumed to always be low.

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

Signatures must conform to strict [verification requirements](https://github.com/dalek-cryptography/ed25519-dalek#validation-criteria) to avoid malleability concerns. While this is not part of the original Ed25519 specification, it has become a growing concern especially in cryptocurrency applications.
