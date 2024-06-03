# Layer 1 Relayer/Bridge Protocol

The Fuel relayer/bridge protocol is a set of rules that govern the interaction between the Fuel blockchain and the
Layer 1 (L1) blockchain (e.g. Ethereum).

The Fuel blockchain can emit messages that will be processed by the smart contract on the L1 blockchain. The smart
contract on the L1 can also emit events that will be processed by the Fuel blockchain.
This is used to move any data between the L1 blockchain and the Fuel blockchain.

## Fuel Message Outbox

The message outbox is the set of messages sent to the L1 blockchain from the Fuel blockchain.

## Fuel Event Inbox

The event inbox is the set of events received from the L1 blockchain by the Fuel blockchain.

The block producer will receive a list of events from the L1 by some relayer, and then include the
merkle root of the events in the block header.

There are two types of events that can be received from the L1:

1. Messages
2. Transactions

### Messages

An arbitrary message sent from the L1 to the Fuel blockchain. This can be used to move assets from the L1
to the Fuel blockchain or send other arbitrary information to the Fuel blockchain.

| name        | type        | description                                                            |
| ----------- | ----------- | ---------------------------------------------------------------------- |
| `sender`    | `bytes[32]` | The sender's identity on the L1 blockchain.                            |
| `recipient` | `bytes[32]` | The recipient on the Fuel blockchain.                                  |
| `nonce`     | `bytes[32]` | Unique identifier assigned by the L1 contract to the message. contract |
| `amount`    | `uint64`    | Amount of the base asset being transferred.                            |
| `data`      | `byte[]`    | Arbitrary message data.                                                |

### Transactions

These are transactions that are submitted on the L1 that must be executed on the Fuel blockchain.
This "Forced Transaction Inclusion" is a security feature that allows participants of the Fuel Blockchain to access
their funds in the (unlikely) event that the Fuel blockchain block production is compromised or malicious, e.g. the
block producer is censoring transactions.

| name                     | type        | description                                                       |
| ------------------------ | ----------- | ----------------------------------------------------------------- |
| `nonce`                  | `bytes[32]` | Unique identifier assigned by the L1 contract to the transaction. |
| `max_gas`                | `uint64`    | Maxium gas allowed on the Fuel blockchain                         |
| `serialized_transaction` | `byte[]`    | Serialized transaction bytes following canonical serialization.   |

The `serialized_transaction` can be any [transaction variant](https://github.com/FuelLabs/fuel-specs/blob/master/src/tx-format/transaction.md) except for the `Mint` transaction, which is exclusively created by the block producer. `Mint` transactions relayed from the L1 will be rejected by the Fuel blockchain.

### Ordering

Correct ordering of L1 events during relay to the Fuel blockchain is crucial. Events are ordered first by L1 block height and then by event index within the block.

The ordering is significant because each time L1 events are included in a Fuel block, a merkle root is generated. This merkle root can later be used to verify the inclusion of an arbitrary event in that block without storing every event explicitly in the block header. Only the merkle root will be included in the [block header](https://github.com/FuelLabs/fuel-specs/blob/master/src/protocol/block-header.md), with the order of events directly influencing its value
