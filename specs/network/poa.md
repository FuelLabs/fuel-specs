# PoA Network

## Consensus Header

Wraps the [application header](../protocol/block_header.md#application-header).

name                   | type       | description
-----------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------
`prevRoot`             | `byte[32]` | [Merkle root](../protocol/cryptographic_primitives.md#binary-merkle-tree) of all previous [consensus header](#consensus-header) hashes (i.e. not including this block).
`applicationHash`      | `byte[32]` | [Hash](../protocol/cryptographic_primitives.md#hashing) of serialized [application header](../protocol/block_header.md#application-header).
`nextValidatorSetRoot` | `byte[32]` | [Merkle root](../protocol/cryptographic_primitives.md#binary-merkle-tree) of the validator set that will vote on the next block.
`nextVotingPower`      | `uint64`   | Voting power of the validator set that will vote on the next block.
`prevCommitHash`       | `byte[32]` | [Hash](../protocol/cryptographic_primitives.md#hashing) of consensus commit for `prevRoot`.

Validators in `nextValidatorSetRoot` and `prevCommitHash` are sorted in descending order by voting power, with ties broken by ascending lexicographic order of 32-byte address.
