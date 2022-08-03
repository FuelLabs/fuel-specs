# Sparse Merkle Tree Test Specifications

## Version

0.1.1

Last updated 2022/07/11

## Abstract

This document outlines a test suite specification that can be used to verify the correctness of a Sparse Merkle Tree's outputs. The scope of this document covers only Sparse Merkle Tree (SMT) implementations that are compliant with [Celestia Sparse Merkle Tree Specification](https://github.com/celestiaorg/celestia-specs/blob/master/src/specs/data_structures.md#sparse-merkle-tree). The goal of this document is to equip SMT library developers with a supplemental indicator of correctness. Libraries implementing an SMT can additionally implement this test suite specification in the code base's native language. Passing all tests in the concrete test suite is an indication of correctness and consistency with the reference specification; however, it is not an absolute guarantee.

The tests described in this document are designed to test features common to most Sparse Merkle Tree implementations. Test specifications are agnostic of the implementation details or language, and therefore take a black-box testing approach. A test specification may provide an example of what a compliant test may look like in the form of pseudocode.

A test specification follows the format:

- Test name
- Test description
- Test inputs
- Test outputs
- Example pseudocode

For a concrete test to comply with its corresponding test specification, the System Under Test (SUT) must take in the prescribed inputs. When the SUT produces the prescribed outputs, the test passes. When the SUT produces any result or error that is not prescribed by the specification, the test fails. For a library to comply with the complete specification described herein, it must implement all test specifications, and each test must pass.

All test specifications assume that the Merkle Tree implementation under test uses the SHA-2-256 hashing algorithm as defined in [FIPS PUB 180-4](https://doi.org/10.6028/NIST.FIPS.180-4) to produce its outputs. The following test cases stipulate a theoretical function `Sum(N)` that takes in a big endian data slice `N` and returns the 32 byte SHA-256 hash of `N`.

## Root Signature Tests

1. [Test Empty Root](#test-empty-root)
2. [Test Update 1](#test-update-1)
3. [Test Update 2](#test-update-2)
4. [Test Update 3](#test-update-3)
5. [Test Update 5](#test-update-5)
6. [Test Update 10](#test-update-10)
7. [Test Update 100](#test-update-100)
8. [Test Update With Repeated Inputs](#test-update-with-repeated-inputs)
9. [Test Update Overwrite Key](#test-update-overwrite-key)
10. [Test Update Union](#test-update-union)
11. [Test Update Sparse Union](#test-update-sparse-union)
12. [Test Update With Empty Data](#test-update-with-empty-data)
13. [Test Update With Empty Data Performs Delete](#test-update-with-empty-data-performs-delete)
14. [Test Update 1 Delete 1](#test-update-1-delete-1)
15. [Test Update 2 Delete 1](#test-update-2-delete-1)
16. [Test Update 10 Delete 5](#test-update-10-delete-5)
17. [Test Delete Non-existent Key](#test-delete-non-existent-key)
18. [Test Interleaved Update Delete](#test-interleaved-update-delete)
19. [Test Delete Sparse Union](#test-delete-sparse-union)

---

### Test Empty Root

**Description**:

Tests the default root given no update or delete operations. The input set is described by `S = {Ø}`.

**Inputs**:

_No inputs_.

**Outputs**:

- The expected root signature: `0x0000000000000000000000000000000000000000000000000000000000000000`

**Example pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
root = smt.root()
expected_root = '0000000000000000000000000000000000000000000000000000000000000000'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 1

**Description**:

Tests the root after performing a single update call with the specified input.

**Inputs**:

1. Update the empty tree with `(K, D)` where leaf key `K = Sum(0u32)` (32 bytes) and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x39f36a7cb4dfb1b46f03d044265df6a491dffc1034121bc1071a34ddce9bb14b`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
root = smt.root()
expected_root = '39f36a7cb4dfb1b46f03d044265df6a491dffc1034121bc1071a34ddce9bb14b'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 2

**Description**:

Tests the root after performing two update calls with the specified inputs.

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree with `(K, D)`, where leaf key `K = Sum(1u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x8d0ae412ca9ca0afcb3217af8bcd5a673e798bd6fd1dfacad17711e883f494cb`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x01"), b"DATA")
root = smt.root()
expected_root = '8d0ae412ca9ca0afcb3217af8bcd5a673e798bd6fd1dfacad17711e883f494cb'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 3

**Description**:

Tests the root after performing three update calls with the specified inputs.

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree with `(K, D)`, where leaf key `K = Sum(1u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
3. Update the tree with `(K, D)`, where leaf key `K = Sum(2u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x52295e42d8de2505fdc0cc825ff9fead419cbcf540d8b30c7c4b9c9b94c268b7`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x01"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x02"), b"DATA")
root = smt.root()
expected_root = '52295e42d8de2505fdc0cc825ff9fead419cbcf540d8b30c7c4b9c9b94c268b7'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 5

**Description**:

Tests the root after performing five update calls with the specified inputs.

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree with `(K, D)`, where leaf key `K = Sum(1u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
3. Update the tree with `(K, D)`, where leaf key `K = Sum(2u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
4. Update the tree with `(K, D)`, where leaf key `K = Sum(3u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
5. Update the tree with `(K, D)`, where leaf key `K = Sum(4u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x108f731f2414e33ae57e584dc26bd276db07874436b2264ca6e520c658185c6b`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..5 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
root = smt.root()
expected_root = '108f731f2414e33ae57e584dc26bd276db07874436b2264ca6e520c658185c6b'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 10

**Description**:

Tests the root after performing 10 update calls with the specified inputs.

**Inputs**:

1. For each `i` in `0..10`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x21ca4917e99da99a61de93deaf88c400d4c082991cb95779e444d43dd13e8849`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..10 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
root = smt.root()
expected_root = '21ca4917e99da99a61de93deaf88c400d4c082991cb95779e444d43dd13e8849'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 100

**Description**:

Tests the root after performing 100 update calls with the specified inputs.

**Inputs**:

1. For each `i` in `0..100`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x82bf747d455a55e2f7044a03536fc43f1f55d43b855e72c0110c986707a23e4d`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..100 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
root = smt.root()
expected_root = '82bf747d455a55e2f7044a03536fc43f1f55d43b855e72c0110c986707a23e4d'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update With Repeated Inputs

**Description**:

Tests the root after performing two update calls with the same inputs. The resulting input set is described by `S = {A} U {A} = {A}`, where `{A}` is the input. This test expects a root signature identical to that produced by [Test Update 1](#test-update-1).

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree again with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x39f36a7cb4dfb1b46f03d044265df6a491dffc1034121bc1071a34ddce9bb14b`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
root = smt.root()
expected_root = '39f36a7cb4dfb1b46f03d044265df6a491dffc1034121bc1071a34ddce9bb14b'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update Overwrite Key

**Description**:

Tests the root after performing two update calls with the same leaf keys but different leaf data. The second update call is expected to overwrite the data originally written by the first update call.

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"CHANGE"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0xdd97174c80e5e5aa3a31c61b05e279c1495c8a07b2a08bca5dbc9fb9774f9457`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x00"), b"CHANGE")
root = smt.root()
expected_root = 'dd97174c80e5e5aa3a31c61b05e279c1495c8a07b2a08bca5dbc9fb9774f9457'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update Union

**Description**:

Tests the root after performing update calls with discontinuous sets of inputs. The resulting input set is described by `S = [0..5) U [10..15) U [20..25)`.

**Inputs**:

1. For each `i` in `0..5`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. For each `i` in `10..15`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
3. For each `i` in `20..25`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0x7e6643325042cfe0fc76626c043b97062af51c7e9fc56665f12b479034bce326`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..5 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 10..15 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 20..25 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
root = smt.root()
expected_root = '7e6643325042cfe0fc76626c043b97062af51c7e9fc56665f12b479034bce326'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update Sparse Union

**Description**:

Tests the root after performing update calls with discontinuous sets of inputs. The resulting input set is described by `S = [0, 2, 4, 6, 8]`.

**Inputs**:

1. For each `i` in `0..5`, update the tree with `(K, D)`, where leaf key `K = Sum(i * 2)` and leaf data `D = b"DATA"` (bytes, UTF-8)

**Outputs**:

- The expected root signature: `0xe912e97abc67707b2e6027338292943b53d01a7fbd7b244674128c7e468dd696`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..5 {
    key = &(i as u32 * 2).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
root = smt.root()
expected_root = 'e912e97abc67707b2e6027338292943b53d01a7fbd7b244674128c7e468dd696'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update With Empty Data

**Description**:

Tests the root after performing one update call with empty data. Updating the empty tree with empty data does not change the root, and the expected root remains the default root. The resulting input set is described by `S = {Ø} U {Ø} = {Ø}`. This test expects a root signature identical to that produced by [Test Empty Root](#test-empty-root).

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and empty leaf data `D = b""` (0 bytes)

**Outputs**:

- The expected root signature: `0x0000000000000000000000000000000000000000000000000000000000000000`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"")
root = smt.root()
expected_root = '0000000000000000000000000000000000000000000000000000000000000000'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update With Empty Data Performs Delete

**Description**:

Tests the root after performing one update call with arbitrary data followed by a second update call on the same key with empty data. Updating a key with empty data is equivalent to calling delete. By deleting the only key, we have an empty tree and expect to arrive at the default root. The resulting input set is described by `S = {0} - {0} = {Ø}`. This test expects a root signature identical to that produced by [Test Empty Root](#test-empty-root).

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree with `(K, D)`, where leaf key `K = Sum(0u32)` and empty leaf data `D = b""` (0 bytes)

**Outputs**:

- The expected root signature: `0x0000000000000000000000000000000000000000000000000000000000000000`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x00"), b"")
root = smt.root()
expected_root = '0000000000000000000000000000000000000000000000000000000000000000'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 1 Delete 1

**Description**:

Tests the root after performing one update call followed by a subsequent delete call on the same key. By deleting the only key, we have an empty tree and expect to arrive at the default root. The resulting input set is described by `S = {0} - {0} = {Ø}`. This test expects a root signature identical to that produced by [Test Empty Root](#test-empty-root).

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Delete `(K)` from the tree, where leaf key `K = Sum(0u32)`  

**Outputs**:

- The expected root signature: `0x0000000000000000000000000000000000000000000000000000000000000000`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.delete(&sum(b"\x00\x00\x00\x00"))
root = smt.root()
expected_root = '0000000000000000000000000000000000000000000000000000000000000000'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 2 Delete 1

**Description**:

Tests the root after performing two update calls followed by a subsequent delete call on the first key. By deleting the second key, we have a tree with only one key remaining, equivalent to a single update. This test expects a root signature identical to that produced by [Test Update 1](#test-update-1).

**Inputs**:

1. Update the empty tree with `(K, D)`, where leaf key `K = Sum(0u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Update the tree with `(K, D)`, where leaf key `K = Sum(1u32)` and leaf data `D = b"DATA"` (bytes, UTF-8)
3. Delete `(K)` from the tree, where leaf key `K = Sum(1u32)`

**Outputs**:

- The expected root signature: `0x39f36a7cb4dfb1b46f03d044265df6a491dffc1034121bc1071a34ddce9bb14b`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
smt.update(&sum(b"\x00\x00\x00\x00"), b"DATA")
smt.update(&sum(b"\x00\x00\x00\x01"), b"DATA")
smt.delete(&sum(b"\x00\x00\x00\x01"))
root = smt.root()
expected_root = '39f36a7cb4dfb1b46f03d044265df6a491dffc1034121bc1071a34ddce9bb14b'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Update 10 Delete 5

**Description**:

Tests the root after performing 10 update calls followed by 5 subsequent delete calls on the latter keys. By deleting the last five keys, we have a tree with the first five keys remaining, equivalent to five updates. This test expects a root signature identical to that produced by [Test Update 5](#test-update-5).

**Inputs**:

1. For each `i` in `0..10`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. For each `i` in `5..10`, delete `(K)` from the tree, where leaf key `K = Sum(i)`

**Outputs**:

- The expected root signature: `0x108f731f2414e33ae57e584dc26bd276db07874436b2264ca6e520c658185c6b`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..10 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 5..10 {
    key = &(i as u32).to_big_endian_bytes()
    smt.delete(&sum(key))
}
root = smt.root()
expected_root = '108f731f2414e33ae57e584dc26bd276db07874436b2264ca6e520c658185c6b'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Delete Non-existent Key

**Description**:

Tests the root after performing five update calls followed by a subsequent delete on a key that is not present in the input set. This test expects a root signature identical to that produced by [Test Update 5](#test-update-5).

**Inputs**:

1. For each `i` in `0..5`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. Delete `(K)` from the tree, where leaf key `K = Sum(1024u32)`

**Outputs**:

- The expected root signature: `0x108f731f2414e33ae57e584dc26bd276db07874436b2264ca6e520c658185c6b`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..5 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
smt.delete(&sum(b"\x00\x00\x04\x00"))

root = smt.root()
expected_root = '108f731f2414e33ae57e584dc26bd276db07874436b2264ca6e520c658185c6b'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Interleaved Update Delete

**Description**:

Tests the root after performing a series of interleaved update and delete calls. The resulting input set is described by `[0..5) U [10..15) U [20..25)`. This test demonstrates the inverse relationship between operations `update` and `delete`. This test expects a root signature identical to that produced by [Test Update Union](#test-update-union).

**Inputs**:

1. For each `i` in `0..10`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. For each `i` in `5..15`, delete `(K)` from the tree, where leaf key `K = Sum(i)` from the tree
3. For each `i` in `10..20`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
4. For each `i` in `15..25`, delete `(K)` from the tree, where leaf key `K = Sum(i)` from the tree
5. For each `i` in `20..30`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
6. For each `i` in `25..35`, delete `(K)` from the tree, where leaf key `K = Sum(i)` from the tree

**Outputs**:

- The expected root signature: `0x7e6643325042cfe0fc76626c043b97062af51c7e9fc56665f12b479034bce326`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..10 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 5..15 {
    key = &(i as u32).to_big_endian_bytes()
    smt.delete(&sum(key))
}
for i in 10..20 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 15..25 {
    key = &(i as u32).to_big_endian_bytes()
    smt.delete(&sum(key))
}
for i in 20..30 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 25..35 {
    key = &(i as u32).to_big_endian_bytes()
    smt.delete(&sum(key))
}
root = smt.root()
expected_root = '7e6643325042cfe0fc76626c043b97062af51c7e9fc56665f12b479034bce326'
expect(hex_encode(root), expected_root).to_be_equal
```

---

### Test Delete Sparse Union

**Description**:

Tests the root after performing delete calls with discontinuous sets of inputs. The resulting input set is described by `S = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] - [1, 3, 5, 7, 9] = [0, 2, 4, 6, 8]`. This test expects a root signature identical to that produced by [Test Update Sparse Union](#test-update-sparse-union).

**Inputs**:

1. For each `i` in `0..10`, update the tree with `(K, D)`, where leaf key `K = Sum(i)` and leaf data `D = b"DATA"` (bytes, UTF-8)
2. For each `i` in `0..5`, delete `(K)` from the tree, where leaf key `K = Sum(i * 2 + 1)`

**Outputs**:

- The expected root signature: `0xe912e97abc67707b2e6027338292943b53d01a7fbd7b244674128c7e468dd696`

**Example Pseudocode**:

```text
smt = SparseMerkleTree.new(Storage.new(), sha256.new())
for i in 0..10 {
    key = &(i as u32).to_big_endian_bytes()
    data = b"DATA"
    smt.update(&sum(key), data)
}
for i in 0..5 {
    key = &(i as u32 * 2 + 1).to_big_endian_bytes()
    smt.delete(&sum(key))
}
root = smt.root()
expected_root = 'e912e97abc67707b2e6027338292943b53d01a7fbd7b244674128c7e468dd696'
expect(hex_encode(root), expected_root).to_be_equal
```
