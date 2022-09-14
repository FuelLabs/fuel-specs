# Block Header

## Application Header

name                 | type       | description
---------------------|------------|-----------------------------------------------------------------------------------------------------
`height`             | `uin64`    | Height of this block.
`timestamp`          | `uin64`    | Time this block was created, in [TAI64](https://cr.yp.to/libtai/tai64.html) format.
`txCount`            | `uint64`   | Number of [transaction](./tx_format.md#transaction)s in this block.
`txRoot`             | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of [transaction](./tx_format.md#transaction)s in this block.
`outputMessagesCount`|`uint64`| Number of [output message](./tx_format.md#outputmessage)s in this block.
`outputMessagesRoot` | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of [output message](./tx_format.md#outputmessage)s in this block.

## Consensus Header

name                   | type       | description
-----------------------|------------|-----------------------------------------------------------------------------------------------------------------------
`prevRoot`           | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of all previous [consensus header](#consensus-header) hashes (i.e. not including this block).
`applicationHash`      | `byte[32]` | [Hash](./cryptographic_primitives.md#hashing) of serialized [application header](#application-header).
`nextValidatorSetRoot` | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of the validator set that will vote on the next block.
`nextVotingPower`      | `uint64`   | Voting power of the validator set that will vote on the next block.
`prevCommitHash`       | `byte[32]` | [Hash](./cryptographic_primitives.md#hashing) of consensus commit for `prevRoot`.

Validators in `nextValidatorSetRoot` and `prevCommitHash` are sorted in descending order by voting power, with ties broken by ascending lexicographic order of 32-byte address.
