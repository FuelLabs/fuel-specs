# Block Header

## Application Header

The application header is a network-agnostic block header. Different [networks](../networks/index.md) may wrap the application header in a consensus header, depending on their consensus protocol.

| name                    | type       | description                                                                                                                                                                                |
|-------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `da_height`             | `uint64`   | Height of the data availability layer up to which (inclusive) input messages are processed.                                                                                                |
| `txCount`               | `uint64`   | Number of [transaction](../tx-format/transaction.md)s in this block.                                                                                                                        |
| `message_receipt_count` | `uint64`   | Number of [output message](../abi/receipts.md#messageout-receipt)s in this block.                                                                                                 |
| `txRoot`                | `byte[32]` | [Merkle root](./cryptographic-primitives.md#binary-merkle-tree) of [transaction](../tx-format/transaction.md)s in this block.                                                               |
|  `message_receipt_root` | `byte[32]` | [Merkle root](./cryptographic-primitives.md#binary-merkle-tree) of [output message](../abi/receipts.md#messageout-receipt)s [`messageId`](../identifiers/utxo-id.md#message-id) in this block. |
