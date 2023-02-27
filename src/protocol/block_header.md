# Block Header

## Application Header

The application header is a network-agnostic block header. Different [networks](../network/index.md) may wrap the application header in a consensus header, depending on their consensus protocol.

name                    | type       | description
------------------------|------------|-----------------------------------------------------------------------------------------------------------------------------------------
`da_height`             | `uint64`   | Height of the data availability layer up to which (inclusive) input messages are processed.
`txCount`               | `uint64`   | Number of [transaction](./tx_format/transaction.md)s in this block.
`message_receipt_count` | `uint64`   | Number of [output message](../protocol/abi/receipts.md#messageout_receipt)s in this block.
`txRoot`                | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of [transaction](./tx_format/transaction.md)s in this block.
`message_receipt_root`  | `byte[32]` | [Merkle root](./cryptographic_primitives.md#binary-merkle-tree) of [output message](./tx_format/output.md#outputmessage)s in this block.
