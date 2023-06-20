# PoA Network

## Consensus Header

Wraps the [application header](../protocol/block-header.md#application-header).

name              | type       | description
------------------|------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------
`prevRoot`        | `byte[32]` | [Merkle root](../protocol/cryptographic-primitives.md#binary-merkle-tree) of all previous consensus header hashes (i.e. not including this block).
`height`          | `uint32`   | Height of this block.
`timestamp`       | `uint64`   | Time this block was created, in [TAI64](https://cr.yp.to/libtai/tai64.html) format.
`applicationHash` | `byte[32]` | [Hash](../protocol/cryptographic-primitives.md#hashing) of serialized [application header](../protocol/block-header.md#application-header) for this block.

Consensus for the consensus header is a single [signature](../protocol/cryptographic-primitives.md#public-key-cryptography) from the authority over the [hash](../protocol/cryptographic-primitives.md#hashing) of the serialized consensus header.

Since the system is secure under the assumption the authority is honest, there is no need for committing to the authority signatures in the header.
