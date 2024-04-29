# Block Header

## Application Header

The application header is a network-agnostic block header. Different [networks](../networks/index.md) may wrap the application header in a consensus header, depending on their consensus protocol.

| name                             | type       | description                                                                                                                                                                                                                                         |
|----------------------------------|------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `da_height`                      | `uint64`   | Height of the data availability layer up to which (inclusive) input messages are processed.                                                                                                                                                         |
| `consensusParametersVersion`     | `uint32`   | The version of the consensus parameters used to execute this block.                                                                                                                                                                                 |
| `stateTransitionBytecodeVersion` | `uint32`   | The version of the state transition bytecode used to execute this block.                                                                                                                                                                            |
| `txCount`                        | `uint16`   | Number of [transaction](../tx-format/transaction.md)s in this block.                                                                                                                                                                                |
| `message_receipt_count`          | `uint32`   | Number of [output message](../abi/receipts.md#messageout-receipt)s in this block.                                                                                                                                                                   |
| `txRoot`                         | `byte[32]` | [Merkle root](./cryptographic-primitives.md#binary-merkle-tree) of [transaction](../tx-format/transaction.md)s in this block.                                                                                                                       |
| `message_outbox_root`            | `byte[32]` | [Merkle root](./cryptographic-primitives.md#binary-merkle-tree) of [output message](../abi/receipts.md#messageout-receipt)s [`messageId`](../identifiers/utxo-id.md#message-id) in this block.                                                      |
| `event_inbox_root`               | `byte[32]` | [Merkle root](./cryptographic-primitives.md#binary-merkle-tree) of all [events](./relayer.md) imported from L1 in this block. The order of the events added to the Merkle tree is the L1 block order, and the index of each event within each block |
