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

| name        | type    | description                                                         |
|-------------|---------|---------------------------------------------------------------------|
| `sender`    | `bytes[32]` | The identity of the sender of the message on the L1                 |
| `recipient` | `bytes[32]` | The recipient of the message on the Fuel Blockchain                 |
| `nonce`     | `bytes[32]` | Unique identifier of the message assigned by the L1 contract                                 |
| `amount`    | `uint64`  | The amount of the base asset transfer                              |
| `data`      | `byte[]`  | Arbitrary message data                                              |

### Transactions

These are transactions that are submitted on the L1 that must be executed on the Fuel blockchain.
This "Forced Transaction Inclusion" is a security feature that allows participants of the Fuel Blockchain to access
their funds in the (unlikely) event that the Fuel blockchain block production is compromised or malicious, e.g. the
block producer is censoring transactions.

| name                     | type      | description                                                                                                                               |
|--------------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `nonce`                  | `bytes[32]` | Unique identifier of the transaction assigned by the L1                                                                          contract |
| `max_gas`                | `uint64`   | The maximum amount of gas allowed to use on  Fuel Blockchain                                                                              |
| `serialized_transaction` | `byte[]`   | The serialized transaction bytes following canonical serialization                                                                        |

The `serialized_transaction` can be any [transaction variant](../tx-format/transaction.md) except the `Mint` transaction, which
is only ever created by the block producer. `Mint` transactions will be rejected by the Fuel blockchain if they are relayed
from the L1.

### Ordering

It is important that the L1 events are ordered correctly when they are relayed to the Fuel blockchain. The events will
be ordered by the L1 block height and then by the index of the event within the block.

The order is important because a merkle root will be generated each time events from L1 are included in a Fuel block.
This merkle root can later be used to prove that an arbitrary event was included on that block without having to store
every event on the block header explicitly. Just the merkle root will be on the [block header](./block-header.md).
The order of the events affects the value of the merkle root.
