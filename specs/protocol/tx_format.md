# Transaction Format

- [Constants](#constants)
- [Transaction](#transaction)
    - [TransactionScript](#transactionscript)
    - [TransactionCreate](#transactioncreate)
- [Input](#input)
    - [InputCoin](#inputcoin)
    - [InputContract](#inputcontract)
- [Output](#output)
    - [OutputCoin](#outputcoin)
    - [OutputContract](#outputcontract)
    - [OutputChange](#outputchange)
    - [OutputVariable](#outputvariable)
    - [OutputContractCreated](#outputcontractcreated)
- [Witness](#witness)

## Constants

| name                        | type     | value | description                                   |
| --------------------------- | -------- | ----- | --------------------------------------------- |
| `GAS_PER_BYTE`              | `uint64` |       | Gas charged per byte of the transaction.      |
| `MAX_GAS_PER_TX`            | `uint64` |       | Maximum gas per transaction.                  |
| `MAX_INPUTS`                | `uint64` | `8`   | Maximum number of inputs.                     |
| `MAX_OUTPUTS`               | `uint64` | `8`   | Maximum number of outputs.                    |
| `MAX_PREDICATE_LENGTH`      | `uint64` |       | Maximum length of predicate, in instructions. |
| `MAX_PREDICATE_DATA_LENGTH` | `uint64` |       | Maximum length of predicate, in bytes.        |
| `MAX_SCRIPT_LENGTH`         | `uint64` |       | Maximum length of script, in instructions.    |
| `MAX_SCRIPT_DATA_LENGTH`    | `uint64` |       | Maximum length of script data, in bytes.      |
| `MAX_WITNESSES`             | `uint64` | `16`  | Maximum number of witnesses.                  |

## Transaction

```
enum  TransactionType : uint8 {
    Script = 0,
    Create = 1,
}
```

| name   | type                                                                                      | description       |
| ------ | ----------------------------------------------------------------------------------------- | ----------------- |
| `type` | `TransactionType`                                                                         | Transaction type. |
| `data` | One of [TransactionScript](#transactionscript) or [TransactionCreate](#transactioncreate) | Transaction data. |

Transaction is invalid if:
* `type > TransactionType.Create`
* `gasLimit > MAX_GAS_PER_TX`
* `blockheight() < maturity`
* `inputsCount > MAX_INPUTS`
* `outputsCount > MAX_OUTPUTS`
* `witnessesCount > MAX_WITNESSES`

When serializing a transaction, fields are serialized as follows (with inner structs serialized recursively):
1. `uint8`, `uint16`, `uint32`, `uint64`: big-endian right-aligned to 8 bytes.
1. `byte[32]`: as-is.
1. `byte[]`: as-is, with padding zeroes aligned to 8 bytes.

When deserializing a transaction, the reverse is done. If there are insufficient bytes or too many bytes, the transaction is invalid.

### TransactionScript

| name               | type                    | description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
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

Transaction is invalid if:
* Any output is of type `OutputType.ContractCreated`
* `scriptLength > MAX_SCRIPT_LENGTH`
* `scriptDataLength > MAX_SCRIPT_DATA_LENGTH`

### TransactionCreate

| name             | type                    | description                                |
| ---------------- | ----------------------- | ------------------------------------------ |
| `gasPrice`       | `uint64`                | Gas price for transaction.                 |
| `gasLimit`       | `uint64`                | Gas limit for transaction.                 |
| `maturity`       | `uint64`                | Block until which tx cannot be included.   |
| `bytecodeLength` | `uint16`                | Contract bytecode length, in instructions. |
| `inputsCount`    | `uint8`                 | Number of inputs.                          |
| `outputsCount`   | `uint8`                 | Number of outputs.                         |
| `witnessesCount` | `uint8`                 | Number of witnesses.                       |
| `salt`           | `byte[32]`              | Salt.                                      |
| `bytecode`       | `byte[]`                | Contract bytecode to create.               |
| `inputs`         | [Input](#input)`[]`     | List of inputs.                            |
| `outputs`        | [Output](#output)`[]`   | List of outputs.                           |
| `witnesses`      | [Witness](#witness)`[]` | List of witnesses.                         |

Transaction is invalid if:
* Any input is of type `InputType.Contract`
* Any output is of type `OutputType.Contract` or `OutputType.Variable`
* More than one output is of type `OutputType.ContractCreated`
* `bytecodeLength * 4 > CONTRACT_MAX_SIZE`

Creates a contract with contract ID `sha256(0x4655454C ++ tx.data.salt ++ root(tx.data.bytecode))`, where `root` is the Merkle root of [the binary Merkle tree](./cryptographic_primitives.md) with each leaf being an 8-byte word of bytecode. If the bytecode is not a multiple of 8 bytes (i.e. if there are an odd number of instructions), the last opcode is padded with 4-byte zero.

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

Transaction is invalid if:
* `type > InputType.Contract`

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

Transaction is invalid if:
* `witnessIndex >= tx.witnessesCount`
* `predicateLength > MAX_PREDICATE_LENGTH`
* `predicateDataLength > MAX_PREDICATE_DATA_LENGTH`

If `h` is the block height the UTXO being spent was created, transaction is invalid if `blockheight() < h + maturity`.

### InputContract

| name         | type       | description  |
| ------------ | ---------- | ------------ |
| `utxoID`     | `byte[32]` | UTXO ID.     |
| `contractID` | `byte[32]` | Contract ID. |

Transaction is invalid if:
* there is not exactly one output of type `OutputType.Contract` with `inputIndex` equal to this input's index

Note: when signing a transaction, `utxoID` is set to zero.

Note: when verifying a predicate, `utxoID` is initialized to zero.

## Output

```
enum  OutputType : uint8 {
    Coin = 0,
    Contract = 1,
    Change = 2,
    Variable = 3,
    ContractCreated = 4,
}
```

| name   | type                                                                                                                                                                                       | description     |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------- |
| `type` | `OutputType`                                                                                                                                                                               | Type of output. |
| `data` | One of [OutputCoin](#outputcoin), [OutputContract](#outputcontract), [OutputChange](#outputchange), [OutputVariable](#outputvariable), or [OutputContractCreated](#outputcontractcreated). | Output data.    |

Transaction is invalid if:
* `type > OutputType.ContractCreated`

### OutputCoin

| name     | type       | description                       |
| -------- | ---------- | --------------------------------- |
| `to`     | `byte[32]` | Receiving address or script hash. |
| `amount` | `uint64`   | Amount of coins to send.          |

### OutputContract

| name         | type       | description                                                    |
| ------------ | ---------- | -------------------------------------------------------------- |
| `inputIndex` | `uint8`    | Index of input contract.                                       |
| `amount`     | `uint64`   | Amount of coins owned by contract after transaction execution. |
| `stateRoot`  | `byte[32]` | State root of contract after transaction execution.            |

Transaction is invalid if:
* `inputIndex >= tx.inputsCount`
* `tx.inputs[inputIndex].type != InputType.Contract`

Note: when signing a transaction, `amount` and `stateRoot` are set to zero.

Note: when verifying a predicate or executing a script, `amount` and `stateRoot` are initialized to the balance and state root of the contract with ID `tx.inputs[inputIndex].contractID`.

### OutputChange

| name     | type       | description                       |
| -------- | ---------- | --------------------------------- |
| `to`     | `byte[32]` | Receiving address or script hash. |
| `amount` | `uint64`   | Amount of coins to send.          |

Note: when signing a transaction, `amount` is set to zero.

Note: when verifying a predicate or executing a script, `amount` is initialized to zero.

This output type indicates that the output's amount may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

### OutputVariable

| name     | type       | description                       |
| -------- | ---------- | --------------------------------- |
| `to`     | `byte[32]` | Receiving address or script hash. |
| `amount` | `uint64`   | Amount of coins to send.          |

Note: when signing a transaction, `to` and `amount` are set to zero.

Note: when verifying a predicate or executing a script, `to` and `amount` are initialized to zero.

This output type indicates that the output's amount and owner may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

### OutputContractCreated

| name         | type       | description  |
| ------------ | ---------- | ------------ |
| `contractID` | `byte[32]` | Contract ID. |

## Witness

| name         | type     | description                       |
| ------------ | -------- | --------------------------------- |
| `dataLength` | `uint16` | Length of witness data, in bytes. |
| `data`       | `byte[]` | Witness data.                     |
