# Transaction ID

The _transaction ID_ (also called _transaction hash_) of a transaction is computed as the [hash](./cryptographic_primitives.md#hashing) of the [serialized transaction](./tx_format/transaction/index.md) with [fields zeroed out for signing](./tx_format/index.md) (see different inputs and outputs for which fields are set to zero), and without witness data. In other words, only all non-witness data is hashed.
