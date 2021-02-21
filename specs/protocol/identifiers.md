# Identifiers

- [Transaction ID](#transaction-id)
- [UTXO ID](#utxo-id)
    - [Coin ID](#coin-id)
    - [Deposit ID](#deposit-id)
    - [Fee ID](#fee-id)

This document defines how to compute the unique identifiers used for transactions and state elements.

## Transaction ID

The _transaction ID_ (also called _transaction hash_) of a transaction is computed as the [hash](./cryptographic_primitives.md#hashing) of the [serialized transaction](./tx_format.md#transaction) with [fields zeroed out for signing](./tx_format.md) (see different inputs and outputs for which fields are set to zero).

## UTXO ID

### Coin ID

The UTXO ID of a transaction's output (i.e. a _coin_) is computed as the [hash](./cryptographic_primitives.md#hashing) of the concatenation of the [transaction ID](#transaction-id) and the output index as a `uint8`.

### Deposit ID

The UTXO ID of a deposit is computed as the [hash](./cryptographic_primitives.md#hashing) of TODO.

### Fee ID

The UTXO ID of collected fees in a block is the block height as a 32-byte big-endian unsigned integer (i.e. the first byte of the 32-byte array is the most significant byte, and so on).
