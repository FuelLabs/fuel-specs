# Transaction Format

- [Constants](#constants)
- [Transaction](#transaction)
- [Input](#input)
    - [InputCoin](#inputcoin)
    - [InputContract](#inputcontract)
- [Output](#output)
    - [OutputCoin](#outputcoin)
    - [OutputVariable](#outputvariable)
    - [OutputContract](#outputcontract)
- [Witness](#witness)

## Constants

| name                        | type     | value | description                                   |
| --------------------------- | -------- | ----- | --------------------------------------------- |
| `GAS_PER_BYTE`              | `uint64` |       | Gas charged per byte of the transaction.      |
| `MAX_INPUTS`                | `uint64` | `8`   | Maximum number of inputs.                     |
| `MAX_OUTPUTS`               | `uint64` | `8`   | Maximum number of outputs.                    |
| `MAX_PREDICATE_LENGTH`      | `uint64` |       | Maximum length of predicate, in instructions. |
| `MAX_PREDICATE_DATA_LENGTH` | `uint64` |       | Maximum length of predicate, in bytes.        |
| `MAX_SCRIPT_LENGTH`         | `uint64` |       | Maximum length of script, in instructions.    |
| `MAX_SCRIPT_DATA_LENGTH`    | `uint64` |       | Maximum length of script data, in bytes.      |
| `MAX_WITNESSES`             | `uint64` | `16`  | Maximum number of witnesses.                  |

## Transaction

| name               | type                    | description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| `version`          | `uint32`                | Transaction version. Always `0`.         |
| `gasPrice`         | `uint64`                | Gas price for transaction.               |
| `gasLimit`         | `uint64`                | Gas limit for transaction.               |
| `maturity`         | `uint64`                | Block until which tx cannot be included. |
| `scriptLength`     | `uint16`                | Script length, in instructions.          |
| `scriptDataLength` | `uint16`                | Length of script input data, in bytes.   |
| `inputsCount`      | `uint8`                 | Number of inputs.                        |
| `outputsCount`     | `uint8`                 | Number of outputs.                       |
| `witnessesCount`   | `uint8`                 | Number of witnesses.                     |
| `script`           | `byte[]`                | Script to execute.                       |
| `scriptData`       | `byte[]`                | Script input data (parameters).          |
| `inputs`           | [Input](#input)`[]`     | List of inputs.                          |
| `outputs`          | [Output](#output)`[]`   | List of outputs.                         |
| `witnesses`        | [Witness](#witness)`[]` | List of witnesses.                       |

Transaction is invalid if `blockheight() < maturity`.

When serializing a transaction, fields are serialized as follows (with inner structs serialized recursively):
1. `uint8`, `uint16`, `uint32`, `uint64`: big-endian right-aligned to 8 bytes.
1. `byte[32]`: as-is.
1. `byte[]`: as-is, with padding zeroes aligned to 8 bytes.

## Input

```
enum  InputType : uint8 {
    Coin = 0,
    Contract = 1,
}
```

| name   | type                                                              | description    |
| ------ | ----------------------------------------------------------------- | -------------- |
| `type` | `InputType`                                                       | Type of input. |
| `data` | One of [InputCoin](#inputcoin) or [InputContract](#inputcontract) | Input data.    |

### InputCoin

| name                  | type       | description                                                            |
| --------------------- | ---------- | ---------------------------------------------------------------------- |
| `utxoID`              | `byte[32]` | UTXO ID.                                                               |
| `witnessIndex`        | `uint8`    | Index of witness that authorizes spending the coin.                    |
| `maturity`            | `uint64`   | UTXO being spent must have been created at least this many blocks ago. |
| `predicateLength`     | `uint16`   | Length of predicate, in instructions.                                  |
| `predicateDataLength` | `uint16`   | Length of predicate input data, in bytes.                              |
| `predicate`           | `byte[]`   | Predicate bytecode.                                                    |
| `predicateData`       | `byte[]`   | Predicate input data (parameters).                                     |

If `h` is the block height the UTXO being spent was created, transaction is invalid if `blockheight() < h + maturity`.

### InputContract

| name         | type       | description  |
| ------------ | ---------- | ------------ |
| `utxoID`     | `byte[32]` | UTXO ID.     |
| `contractID` | `byte[32]` | Contract ID. |

Note: when signing a transaction, `utxoID` is set to zero.

## Output

```
enum  OutputType : uint8 {
    Coin = 0,
    Variable = 1,
    Contract = 2,
}
```

| name   | type                                                                  | description     |
| ------ | --------------------------------------------------------------------- | --------------- |
| `type` | `OutputType`                                                          | Type of output. |
| `data` | One of [OutputCoin](#outputcoin) or [OutputContract](#outputcontract) | Output data.    |

### OutputCoin

| name     | type       | description                       |
| -------- | ---------- | --------------------------------- |
| `to`     | `byte[32]` | Receiving address or script hash. |
| `amount` | `uint64`   | Amount of coins to send.          |

### OutputVariable

| name     | type       | description                       |
| -------- | ---------- | --------------------------------- |
| `to`     | `byte[32]` | Receiving address or script hash. |
| `amount` | `uint64`   | Amount of coins to send.          |

Note: when signing a transaction, `to` and `amount` are set to zero.

Note: when executing a transaction, `to` and `amount` are set to zero.

This output type indicates that the output's amount and owner may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

### OutputContract

| name         | type       | description                                                    |
| ------------ | ---------- | -------------------------------------------------------------- |
| `inputIndex` | `uint8`    | Index of input contract.                                       |
| `amount`     | `uint64`   | Amount of coins owned by contract after transaction execution. |
| `stateRoot`  | `byte[32]` | State root of contract after transaction execution.            |

Note: when signing a transaction, `amount` and `stateRoot` are set to zero.

Note: when executing a transaction, `amount` and `stateRoot` are set to zero.

## Witness

| name         | type     | description                       |
| ------------ | -------- | --------------------------------- |
| `dataLength` | `uint16` | Length of witness data, in bytes. |
| `data`       | `byte[]` | Witness data.                     |
