# Block Header

## Application Header

The application header is a network-agnostic block header. Different [networks](../network/index.md) may wrap the application header in a consensus header, depending on their consensus protocol.

name                  | type       | description
----------------------|------------|----------------------------------------------------------------------------------------------------------------------------------
`height`              | `uint64`   | Height of this block.
`timestamp`           | `uint64`   | Time this block was created, in [TAI64](https://cr.yp.to/libtai/tai64.html) format.
`txCount`             | `uint64`   | Number of [transaction](./tx_format.md#transaction)s in this block.
`outputMessagesCount` | `uint64`   | Number of [output message](./tx_format.md#outputmessage)s in this block.
`txRoot`              | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of [transaction](./tx_format.md#transaction)s in this block.
`outputMessagesRoot`  | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of [output message](./tx_format.md#outputmessage)s in this block.
