# Identifiers

- [Transaction ID](#transaction-id)
- [UTXO ID](#utxo-id)

This document defines how to compute the unique identifiers used for transactions and state elements.

## Transaction ID

The _transaction ID_ (also called _transaction hash_) of a transaction is computed as the [hash](./cryptographic_primitives.md#hashing) of the [serialized transaction](./tx_format.md#transaction) with [fields zeroed out for signing](./tx_format.md) (see different inputs and outputs for which fields are set to zero).

## UTXO ID

The UTXO ID of a transaction's output is computed as the [hash](./cryptographic_primitives.md#hashing) of the concatenation of the [transaction ID](#transaction-id) and the output index as a `uint8`.

The UTXO ID of a deposit is computed as the [hash](./cryptographic_primitives.md#hashing) of TODO.
