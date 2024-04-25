# UTXO ID

## Coin ID

Is represented as an _outpoint_: a pair of [transaction ID](./transaction-id.md) as `byte[32]` and output index as a `uint16`.

## Message ID

The ID of a message is computed as the [hash](../protocol/cryptographic-primitives.md#hashing) of:

1. the sender address as `byte[32]`,
1. the recipient address as `byte[32]`,
1. the [Message nonce](#message-nonce) as `byte[32]`,
1. the amount being sent with the message as `uint64`,
1. the message data as `byte[]`

`hash(byte[32] ++ byte[32] ++ byte[32] ++ uint64 ++ byte[])`. The address values are serialized as a byte array of length 32 left-padded with zeroes, and all other value types are serialized according to the standard [transaction serialization](../tx-format/transaction.md). Note that the message data length is not included since there is only one dynamically sized field and can be implicitly determined by the hash preimage size.

### Message Nonce

The nonce value for `InputMessage` is determined by the sending system and is published at the time the message is sent. The nonce value for `OutputMessage` is computed as the [hash](../protocol/cryptographic-primitives.md#hashing) of the [Transaction ID](./transaction-id.md) that emitted the message and the index of the message receipt `uint16` (with canonical encoding): `hash(byte[32] ++ canonical(uint16))`.

## Fee ID

The UTXO ID of collected fees in a block is the block height as a 32-byte big-endian unsigned integer (i.e. the first byte of the 32-byte array is the most significant byte, and so on).
