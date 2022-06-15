# Identifiers

- [Transaction ID](#transaction-id)
- [Contract ID](#contract-id)
- [UTXO ID](#utxo-id)
  - [Coin ID](#coin-id)
  - [Input Message ID](#input-message-id)
  - [Output Message ID](#output-message-id)
    - [Output Message Nonce](#output-message-nonce)
  - [Fee ID](#fee-id)

This document defines how to compute unique identifiers.

## Transaction ID

The _transaction ID_ (also called _transaction hash_) of a transaction is computed as the [hash](./cryptographic_primitives.md#hashing) of the [serialized transaction](./tx_format.md#transaction) with [fields zeroed out for signing](./tx_format.md) (see different inputs and outputs for which fields are set to zero), and without witness data. In other words, only all non-witness data is hashed.

## Contract ID

For a transaction of type `TransactionType.Create`, `tx`, the contract ID is `sha256(0x4655454C ++ tx.data.salt ++ root(tx.data.witnesses[bytecodeWitnessIndex].data) ++ root_smt(tx.storageSlots))`, where `root` is the Merkle root of [the binary Merkle tree](./cryptographic_primitives.md#binary-merkle-tree) with each leaf being an 8-byte word of two instructions, and `root_smt` is the [Sparse Merkle tree](./cryptographic_primitives.md#sparse-merkle-tree) root of the provided key-value pairs. If the bytecode is not a multiple of 8 bytes (i.e. if there are an odd number of instructions), the last instruction is padded with 4-byte zero.

## UTXO ID

### Coin ID

Is represented as an _outpoint_: a pair of [transaction ID](#transaction-id) as `byte[32]` and output index as a `uint8`.

### Input Message ID

The ID of an input message is computed as the [hash](./cryptographic_primitives.md#hashing) of:

1. the sender address as `byte[32]`,
1. recipient address as `byte[32]`,
1. the message owner (either address or predicate root) as `byte[32]`,
1. the amount being sent with the message as `uint64`,
1. the message nonce as `uint64`,
1. the message data as `byte[]`

`hash(byte[32] ++ byte[32] ++ byte[32] ++ uint64 ++ uint64 ++ byte[])`. The address values are serialized as a byte array of length 32 left-padded with zeroes, and all other value types are serialized according to the standard [transaction serialization](./tx_format.md#transaction). Note that the message data length is not included since there is only one dynamically sized field and can be implicitly determined by the hash preimage size.

### Output Message ID

The ID of an input message is computed as the [hash](./cryptographic_primitives.md#hashing) of:

1. the sender address as `byte[32]`,
1. recipient address as `byte[32]`,
1. the [OutputMessage nonce](#output-message-nonce) as `byte[32]`,
1. the amount being sent with the message as `uint64`,
1. the message data as `byte[]`

`hash(byte[32] ++ byte[32] ++ byte[32] ++ uint64 ++ byte[])`. The address values are serialized as a byte array of length 32 left-padded with zeroes, and all other value types are serialized according to the standard [transaction serialization](./tx_format.md#transaction). Note that the message data length is not included since there is only one dynamically sized field and can be implicitly determined by the hash preimage size.

#### Output Message Nonce

The nonce value for `OutputMessage` is computed as the [hash](./cryptographic_primitives.md#hashing) of the [Transaction ID](#transaction-id) that the message is an output for and the index of the output as a `uint8`. `hash(byte[32] ++ uint8)`

### Fee ID

The UTXO ID of collected fees in a block is the block height as a 32-byte big-endian unsigned integer (i.e. the first byte of the 32-byte array is the most significant byte, and so on).
