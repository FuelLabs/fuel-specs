# Transaction Format

- [Constants](#constants)
- [TransactionType](#transactiontype)
- [Transaction](#transaction)
  - [TransactionScript](#transactionscript)
  - [TransactionCreate](#transactioncreate)
- [InputType](#inputtype)
- [Input](#input)
  - [InputCoin](#inputcoin)
  - [InputContract](#inputcontract)
- [OutputType](#outputtype)
- [Output](#output)
  - [OutputCoin](#outputcoin)
  - [OutputContract](#outputcontract)
  - [OutputWithdrawal](#outputwithdrawal)
  - [OutputChange](#outputchange)
  - [OutputVariable](#outputvariable)
  - [OutputContractCreated](#outputcontractcreated)
- [Witness](#witness)
- [TXPointer](#txpointer)

## Constants

| name                        | type     | value           | description                                   |
|-----------------------------|----------|-----------------|-----------------------------------------------|
| `GAS_PER_BYTE`              | `uint64` |                 | Gas charged per byte of the transaction.      |
| `GAS_PRICE_FACTOR`          | `uint64` | `1,000,000,000` | Unit factor for gas price.                    |
| `MAX_GAS_PER_TX`            | `uint64` |                 | Maximum gas per transaction.                  |
| `MAX_INPUTS`                | `uint64` | `8`             | Maximum number of inputs.                     |
| `MAX_OUTPUTS`               | `uint64` | `8`             | Maximum number of outputs.                    |
| `MAX_PREDICATE_LENGTH`      | `uint64` |                 | Maximum length of predicate, in instructions. |
| `MAX_PREDICATE_DATA_LENGTH` | `uint64` |                 | Maximum length of predicate data, in bytes.   |
| `MAX_SCRIPT_LENGTH`         | `uint64` |                 | Maximum length of script, in instructions.    |
| `MAX_SCRIPT_DATA_LENGTH`    | `uint64` |                 | Maximum length of script data, in bytes.      |
| `MAX_STORAGE_SLOTS`         | `uint16` | `255`           | Maximum number of initial storage slots.      |
| `MAX_WITNESSES`             | `uint64` | `16`            | Maximum number of witnesses.                  |

## TransactionType

```
enum  TransactionType : uint8 {
    Script = 0,
    Create = 1,
}
```

## Transaction

| name   | type                                                                                      | description       |
|--------|-------------------------------------------------------------------------------------------|-------------------|
| `type` | [TransactionType](#transactiontype)                                                       | Transaction type. |
| `data` | One of [TransactionScript](#transactionscript) or [TransactionCreate](#transactioncreate) | Transaction data. |

Transaction is invalid if:

- `type > TransactionType.Create`
- `gasLimit > MAX_GAS_PER_TX`
- `blockheight() < maturity`
- `inputsCount > MAX_INPUTS`
- `outputsCount > MAX_OUTPUTS`
- `witnessesCount > MAX_WITNESSES`
- No inputs are of type `InputType.Coin`
- More than one output is of type `OutputType.Change` for any asset ID in the input set
- Any output is of type `OutputType.Change` for any asset ID not in the input set

When serializing a transaction, fields are serialized as follows (with inner structs serialized recursively):

1. `uint8`, `uint16`, `uint32`, `uint64`: big-endian right-aligned to 8 bytes.
1. `byte[32]`: as-is.
1. `byte[]`: as-is, with padding zeroes aligned to 8 bytes.

When deserializing a transaction, the reverse is done. If there are insufficient bytes or too many bytes, the transaction is invalid.

### TransactionScript

```
enum  ReceiptType : uint8 {
    Call = 0,
    Return = 1,
    ReturnData = 2,
    Panic = 3,
    Revert = 4,
    Log = 5,
    LogData = 6,
    Transfer = 7,
    TransferOut = 8,
    ScriptResult = 9,
}
```

| name               | type                    | description                              |
|--------------------|-------------------------|------------------------------------------|
| `gasPrice`         | `uint64`                | Gas price for transaction.               |
| `gasLimit`         | `uint64`                | Gas limit for transaction.               |
| `maturity`         | `uint32`                | Block until which tx cannot be included. |
| `scriptLength`     | `uint16`                | Script length, in instructions.          |
| `scriptDataLength` | `uint16`                | Length of script input data, in bytes.   |
| `inputsCount`      | `uint8`                 | Number of inputs.                        |
| `outputsCount`     | `uint8`                 | Number of outputs.                       |
| `witnessesCount`   | `uint8`                 | Number of witnesses.                     |
| `receiptsRoot`     | `byte[32]`              | Merkle root of receipts.                 |
| `script`           | `byte[]`                | Script to execute.                       |
| `scriptData`       | `byte[]`                | Script input data (parameters).          |
| `inputs`           | [Input](#input)`[]`     | List of inputs.                          |
| `outputs`          | [Output](#output)`[]`   | List of outputs.                         |
| `witnesses`        | [Witness](#witness)`[]` | List of witnesses.                       |

Transaction is invalid if:

- Any output is of type `OutputType.ContractCreated`
- `scriptLength > MAX_SCRIPT_LENGTH`
- `scriptDataLength > MAX_SCRIPT_DATA_LENGTH`

Note: when signing a transaction, `receiptsRoot` is set to zero.

Note: when verifying a predicate, `receiptsRoot` is initialized to zero.

Note: when executing a script, `receiptsRoot` is initialized to zero.

The receipts root `receiptsRoot` is the root of the [binary Merkle tree](./cryptographic_primitives.md#binary-merkle-tree) of receipts. If there are no receipts, its value is set to the root of the empty tree, i.e. `0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`.

### TransactionCreate

| name                   | type                      | description                                       |
|------------------------|---------------------------|---------------------------------------------------|
| `gasPrice`             | `uint64`                  | Gas price for transaction.                        |
| `gasLimit`             | `uint64`                  | Gas limit for transaction.                        |
| `maturity`             | `uint32`                  | Block until which tx cannot be included.          |
| `bytecodeLength`       | `uint16`                  | Contract bytecode length, in instructions.        |
| `bytecodeWitnessIndex` | `uint8`                   | Witness index of contract bytecode to create.     |
| `storageSlotsCount`    | `uint16`                  | Number of storage slots to initialize.            |
| `inputsCount`          | `uint8`                   | Number of inputs.                                 |
| `outputsCount`         | `uint8`                   | Number of outputs.                                |
| `witnessesCount`       | `uint8`                   | Number of witnesses.                              |
| `salt`                 | `byte[32]`                | Salt.                                             |
| `storageSlots`         | `(byte[32], byte[32]])[]` | List of storage slots to initialize (key, value). |
| `inputs`               | [Input](#input)`[]`       | List of inputs.                                   |
| `outputs`              | [Output](#output)`[]`     | List of outputs.                                  |
| `witnesses`            | [Witness](#witness)`[]`   | List of witnesses.                                |

Transaction is invalid if:

- Any input is of type `InputType.Contract`
- Any output is of type `OutputType.Contract` or `OutputType.Variable`
- More than one output is of type `OutputType.Change` with `asset_id` of zero
- Any output is of type `OutputType.Change` with non-zero `asset_id`
- It does not have exactly one output of type `OutputType.ContractCreated`
- `bytecodeLength * 4 > CONTRACT_MAX_SIZE`
- `tx.data.witnesses[bytecodeWitnessIndex].dataLength != bytecodeLength * 4`
- `bytecodeWitnessIndex >= tx.witnessesCount`
- The keys of `storageSlots` are not in ascending lexicographic order
- The computed contract ID (see below) is not equal to the `contractID` of the one `OutputType.ContractCreated` output
- `storageSlotsCount > MAX_STORAGE_SLOTS`
- The [Sparse Merkle tree](./cryptographic_primitives.md#sparse-merkle-tree) root of `storageSlots` is not equal to the `stateRoot` of the one `OutputType.ContractCreated` output

Creates a contract with contract ID as computed [here](./identifiers.md#contract-id).

## InputType

```
enum  InputType : uint8 {
    Coin = 0,
    Contract = 1,
}
```

## Input

| name   | type                                                              | description    |
|--------|-------------------------------------------------------------------|----------------|
| `type` | [InputType](#inputtype)                                           | Type of input. |
| `data` | One of [InputCoin](#inputcoin) or [InputContract](#inputcontract) | Input data.    |

Transaction is invalid if:

- `type > InputType.Contract`

### InputCoin

| name                  | type                    | description                                                            |
|-----------------------|-------------------------|------------------------------------------------------------------------|
| `txID`                | `byte[32]`              | Hash of transaction.                                                   |
| `outputIndex`         | `uint8`                 | Index of transaction output.                                           |
| `owner`               | `byte[32]`              | Owning address or predicate hash.                                      |
| `amount`              | `uint64`                | Amount of coins.                                                       |
| `asset_id`            | `byte[32]`              | Asset ID of the coins.                                                 |
| `txPointer`           | [TXPointer](#txpointer) | Points to the TX whose output is being spent.                          |
| `witnessIndex`        | `uint8`                 | Index of witness that authorizes spending the coin.                    |
| `maturity`            | `uint32`                | UTXO being spent must have been created at least this many blocks ago. |
| `predicateLength`     | `uint16`                | Length of predicate, in instructions.                                  |
| `predicateDataLength` | `uint16`                | Length of predicate input data, in bytes.                              |
| `predicate`           | `byte[]`                | Predicate bytecode.                                                    |
| `predicateData`       | `byte[]`                | Predicate input data (parameters).                                     |

Transaction is invalid if:

- `witnessIndex >= tx.witnessesCount`
- `predicateLength > MAX_PREDICATE_LENGTH`
- `predicateDataLength > MAX_PREDICATE_DATA_LENGTH`
- If `predicateLength > 0`; the computed predicate root (see below) is not equal `owner`

If `h` is the block height the UTXO being spent was created, transaction is invalid if `blockheight() < h + maturity`.

Note: when signing a transaction, `txPointer` is set to zero.

Note: when verifying a predicate, `txPointer` is initialized to zero.

Note: when executing a script, `txPointer` is initialized to zero.

The predicate root is computed identically to the contract root, used to compute the contract ID, [here](./identifiers.md#contract-id).

### InputContract

| name          | type                    | description                                                             |
|---------------|-------------------------|-------------------------------------------------------------------------|
| `txID`        | `byte[32]`              | Hash of transaction.                                                    |
| `outputIndex` | `uint8`                 | Index of transaction output.                                            |
| `balanceRoot` | `byte[32]`              | Root of amount of coins owned by contract before transaction execution. |
| `stateRoot`   | `byte[32]`              | State root of contract before transaction execution.                    |
| `txPointer`   | [TXPointer](#txpointer) | Points to the TX whose output is being spent.                           |
| `contractID`  | `byte[32]`              | Contract ID.                                                            |

Transaction is invalid if:

- there is not exactly one output of type `OutputType.Contract` with `inputIndex` equal to this input's index

Note: when signing a transaction, `txID`, `outputIndex`, `balanceRoot`, `stateRoot`, and `txPointer` are set to zero.

Note: when verifying a predicate, `txID`, `outputIndex`, `balanceRoot`, `stateRoot`, and `txPointer` are initialized to zero.

Note: when executing a script, `txID`, `outputIndex`, `balanceRoot`, and `stateRoot` are initialized to the transaction ID, output index, amount, and state root of the contract with ID `contractID`, and `txPointer` is initialized to zero.

## OutputType

```
enum  OutputType : uint8 {
    Coin = 0,
    Contract = 1,
    Withdrawal = 2,
    Change = 3,
    Variable = 4,
    ContractCreated = 5,
}
```

## Output

| name   | type                                                                                                                                                                                                                             | description     |
|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|
| `type` | [OutputType](#outputtype)                                                                                                                                                                                                        | Type of output. |
| `data` | One of [OutputCoin](#outputcoin), [OutputContract](#outputcontract), [OutputWithdrawal](#outputwithdrawal) [OutputChange](#outputchange), [OutputVariable](#outputvariable), or [OutputContractCreated](#outputcontractcreated). | Output data.    |

Transaction is invalid if:

- `type > OutputType.ContractCreated`

### OutputCoin

| name       | type       | description                          |
|------------|------------|--------------------------------------|
| `to`       | `byte[32]` | Receiving address or predicate hash. |
| `amount`   | `uint64`   | Amount of coins to send.             |
| `asset_id` | `byte[32]` | Asset ID of coins.                   |

### OutputContract

| name          | type       | description                                                            |
|---------------|------------|------------------------------------------------------------------------|
| `inputIndex`  | `uint8`    | Index of input contract.                                               |
| `balanceRoot` | `byte[32]` | Root of amount of coins owned by contract after transaction execution. |
| `stateRoot`   | `byte[32]` | State root of contract after transaction execution.                    |

Transaction is invalid if:

- `inputIndex >= tx.inputsCount`
- `tx.inputs[inputIndex].type != InputType.Contract`

Note: when signing a transaction, `balanceRoot` and `stateRoot` are set to zero.

Note: when verifying a predicate, `balanceRoot` and `stateRoot` are initialized to zero.

Note: when executing a script, `balanceRoot` and `stateRoot` are initialized to the balance root and state root of the contract with ID `tx.inputs[inputIndex].contractID`.

The balance root `balanceRoot` is the root of the [SMT](./cryptographic_primitives.md#sparse-merkle-tree) of balance leaves. Each balance is a `uint64`, keyed by asset ID (a `byte[32]`).

The state root `stateRoot` is the root of the [SMT](./cryptographic_primitives.md#sparse-merkle-tree) of storage slots. Each storage slot is a `byte[32]`, keyed by a `byte[32]`.

### OutputWithdrawal

| name       | type       | description                  |
|------------|------------|------------------------------|
| `to`       | `byte[32]` | Receiving address.           |
| `amount`   | `uint64`   | Amount of coins to withdraw. |
| `asset_id` | `byte[32]` | Asset ID of coins.           |

This output type is unspendable and can be pruned form the UTXO set.

### OutputChange

| name       | type       | description                          |
|------------|------------|--------------------------------------|
| `to`       | `byte[32]` | Receiving address or predicate hash. |
| `amount`   | `uint64`   | Amount of coins to send.             |
| `asset_id` | `byte[32]` | Asset ID of coins.                   |

Transaction is invalid if:

- any other output has type `OutputType.OutputChange` and asset ID `asset_id` (i.e. only one change output per asset ID is allowed)

Note: when signing a transaction, `amount` is set to zero.

Note: when verifying a predicate or executing a script, `amount` is initialized to zero.

This output type indicates that the output's amount may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

### OutputVariable

| name       | type       | description                          |
|------------|------------|--------------------------------------|
| `to`       | `byte[32]` | Receiving address or predicate hash. |
| `amount`   | `uint64`   | Amount of coins to send.             |
| `asset_id` | `byte[32]` | Asset ID of coins.                   |

Note: when signing a transaction, `to`, `amount`, and `asset_id` are set to zero.

Note: when verifying a predicate or executing a script, `to`, `amount`, and `asset_id` are initialized to zero.

This output type indicates that the output's amount and owner may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

### OutputContractCreated

| name         | type       | description                     |
|--------------|------------|---------------------------------|
| `contractID` | `byte[32]` | Contract ID.                    |
| `stateRoot`  | `byte[32]` | Initial state root of contract. |

## Witness

| name         | type     | description                       |
|--------------|----------|-----------------------------------|
| `dataLength` | `uint16` | Length of witness data, in bytes. |
| `data`       | `byte[]` | Witness data.                     |

## TXPointer

| name          | type     | description        |
|---------------|----------|--------------------|
| `blockHeight` | `uint32` | Block height.      |
| `txIndex`     | `uint16` | Transaction index. |
