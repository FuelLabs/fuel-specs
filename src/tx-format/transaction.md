# Transaction

```c++
enum TransactionType : uint8 {
    Script = 0,
    Create = 1,
    Mint = 2,
}
```

| name   | type                                                                                                                            | description       |
|--------|---------------------------------------------------------------------------------------------------------------------------------|-------------------|
| `type` | [TransactionType](#transaction)                                                                                                 | Transaction type. |
| `data` | One of [TransactionScript](#transactionscript), [TransactionCreate](#transactioncreate), or [TransactionMint](#transactionmint) | Transaction data. |

Transaction is invalid if:

- `type > TransactionType.Create`
- `inputsCount > MAX_INPUTS`
- `outputsCount > MAX_OUTPUTS`
- `witnessesCount > MAX_WITNESSES`
- No inputs are of type `InputType.Coin` or `InputType.Message` with `input.dataLength` == 0
- More than one output is of type `OutputType.Change` for any asset ID in the input set
- Any output is of type `OutputType.Change` for any asset ID not in the input set
- More than one input of type `InputType.Coin` for any [Coin ID](../identifiers/utxo-id.md#coin-id) in the input set
- More than one input of type `InputType.Contract` for any [Contract ID](../identifiers/utxo-id.md#contract-id) in the input set
- More than one input of type `InputType.Message` for any [Message ID](../identifiers/utxo-id.md#message-id) in the input set

When serializing a transaction, fields are serialized as follows (with inner structs serialized recursively):

1. `uint8`, `uint16`, `uint32`, `uint64`: big-endian right-aligned to 8 bytes.
1. `byte[32]`: as-is.
1. `byte[]`: as-is, with padding zeroes aligned to 8 bytes.

When deserializing a transaction, the reverse is done. If there are insufficient bytes or too many bytes, the transaction is invalid.

## TransactionScript

```c++
enum ReceiptType : uint8 {
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
    MessageOut = 10,
    Mint = 11,
    Burn = 12,
}
```

| name               | type                        | description                            |
|--------------------|-----------------------------|----------------------------------------|
| `gasPrice`         | `uint64`                    | Gas price for transaction.             |
| `scriptLength`     | `uint16`                    | Script length, in instructions.        |
| `scriptDataLength` | `uint16`                    | Length of script input data, in bytes. |
| `policyCount`      | `uint8`                     | Number of policies.                    |
| `inputsCount`      | `uint8`                     | Number of inputs.                      |
| `outputsCount`     | `uint8`                     | Number of outputs.                     |
| `witnessesCount`   | `uint8`                     | Number of witnesses.                   |
| `receiptsRoot`     | `byte[32]`                  | Merkle root of receipts.               |
| `policies`         | [Policy](./policy.md)`[]`   | List of policies.                      |
| `script`           | `byte[]`                    | Script to execute.                     |
| `scriptData`       | `byte[]`                    | Script input data (parameters).        |
| `inputs`           | [Input](./input.md)`[]`     | List of inputs.                        |
| `outputs`          | [Output](./output.md)`[]`   | List of outputs.                       |
| `witnesses`        | [Witness](./witness.md)`[]` | List of witnesses.                     |

Given helper `len()` that returns the number of bytes of a field.

Transaction is invalid if:

- Any output is of type `OutputType.ContractCreated`
- `scriptLength > MAX_SCRIPT_LENGTH`
- `scriptDataLength > MAX_SCRIPT_DATA_LENGTH`
- `scriptLength * 4 != len(script)`
- `scriptDataLength != len(scriptData)`

> **Note:** when signing a transaction, `receiptsRoot` is set to zero.
>
> **Note:** when verifying a predicate, `receiptsRoot` is initialized to zero.
>
> **Note:** when executing a script, `receiptsRoot` is initialized to zero.

The receipts root `receiptsRoot` is the root of the [binary Merkle tree](../protocol/cryptographic-primitives.md#binary-merkle-tree) of receipts. If there are no receipts, its value is set to the root of the empty tree, i.e. `0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`.

## TransactionCreate

| name                   | type                        | description                                       |
|------------------------|-----------------------------|---------------------------------------------------|
| `gasPrice`             | `uint64`                    | Gas price for transaction.                        |
| `bytecodeLength`       | `uint16`                    | Contract bytecode length, in instructions.        |
| `bytecodeWitnessIndex` | `uint8`                     | Witness index of contract bytecode to create.     |
| `policyCount`          | `uint8`                     | Number of policies.                               |
| `storageSlotsCount`    | `uint16`                    | Number of storage slots to initialize.            |
| `inputsCount`          | `uint8`                     | Number of inputs.                                 |
| `outputsCount`         | `uint8`                     | Number of outputs.                                |
| `witnessesCount`       | `uint8`                     | Number of witnesses.                              |
| `salt`                 | `byte[32]`                  | Salt.                                             |
| `policies`             | [Policy](./policy.md)`[]`   | List of policies.                                 |
| `storageSlots`         | `(byte[32], byte[32]])[]`   | List of storage slots to initialize (key, value). |
| `inputs`               | [Input](./input.md)`[]`     | List of inputs.                                   |
| `outputs`              | [Output](./output.md)`[]`   | List of outputs.                                  |
| `witnesses`            | [Witness](./witness.md)`[]` | List of witnesses.                                |

Transaction is invalid if:

- Any input is of type `InputType.Contract` or `InputType.Message` where `input.dataLength > 0`
- Any output is of type `OutputType.Contract` or `OutputType.Variable` or `OutputType.Message`
- More than one output is of type `OutputType.Change` with `asset_id` of zero
- Any output is of type `OutputType.Change` with non-zero `asset_id`
- It does not have exactly one output of type `OutputType.ContractCreated`
- `bytecodeLength * 4 > CONTRACT_MAX_SIZE`
- `tx.data.witnesses[bytecodeWitnessIndex].dataLength != bytecodeLength * 4`
- `bytecodeWitnessIndex >= tx.witnessesCount`
- The keys of `storageSlots` are not in ascending lexicographic order
- The computed contract ID (see below) is not equal to the `contractID` of the one `OutputType.ContractCreated` output
- `storageSlotsCount > MAX_STORAGE_SLOTS`
- The [Sparse Merkle tree](../protocol/cryptographic-primitives.md#sparse-merkle-tree) root of `storageSlots` is not equal to the `stateRoot` of the one `OutputType.ContractCreated` output

Creates a contract with contract ID as computed [here](../identifiers/contract-id.md).

## TransactionMint

The transaction is created by the block producer and is not signed. Since it is not usable outside of block creation or execution, all fields must be fully set upon creation without any zeroing.

| name           | type                         | description                                          |
|----------------|------------------------------|------------------------------------------------------|
| `txPointer`    | [TXPointer](./tx-pointer.md) | The location of the `Mint` transaction in the block. |
| `outputsCount` | `uint8`                      | Number of outputs.                                   |
| `outputs`      | [Output](./output.md)`[]`    | List of outputs.                                     |

Transaction is invalid if:

- Any output is not of type `OutputType.Coin`
- Any two outputs have the same `asset_id`
- `txPointer` is zero or doesn't match the block.
