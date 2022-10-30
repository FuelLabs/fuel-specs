# Input

```c++
enum  InputType : uint8 {
    Coin = 0,
    Contract = 1,
    Message = 2,
}
```

| name   | type                                                                                              | description    |
|--------|---------------------------------------------------------------------------------------------------|----------------|
| `type` | [InputType](#input)                                                                               | Type of input. |
| `data` | One of [InputCoin](#inputcoin), [InputContract](#inputcontract), or [InputMessage](#inputmessage) | Input data.    |

Transaction is invalid if:

- `type > InputType.Message`

## InputCoin

| name                  | type                         | description                                                            |
|-----------------------|------------------------------|------------------------------------------------------------------------|
| `txID`                | `byte[32]`                   | Hash of transaction.                                                   |
| `outputIndex`         | `uint8`                      | Index of transaction output.                                           |
| `owner`               | `byte[32]`                   | Owning address or predicate root.                                      |
| `amount`              | `uint64`                     | Amount of coins.                                                       |
| `asset_id`            | `byte[32]`                   | Asset ID of the coins.                                                 |
| `txPointer`           | [TXPointer](./tx_pointer.md) | Points to the TX whose output is being spent.                          |
| `witnessIndex`        | `uint8`                      | Index of witness that authorizes spending the coin.                    |
| `maturity`            | `uint32`                     | UTXO being spent must have been created at least this many blocks ago. |
| `predicateLength`     | `uint16`                     | Length of predicate, in instructions.                                  |
| `predicateDataLength` | `uint16`                     | Length of predicate input data, in bytes.                              |
| `predicate`           | `byte[]`                     | Predicate bytecode.                                                    |
| `predicateData`       | `byte[]`                     | Predicate input data (parameters).                                     |

Given helper `len()` that returns the number of bytes of a field.

Transaction is invalid if:

- `witnessIndex >= tx.witnessesCount`
- `predicateLength > MAX_PREDICATE_LENGTH`
- `predicateDataLength > MAX_PREDICATE_DATA_LENGTH`
- If `predicateLength > 0`; the computed predicate root (see below) is not equal `owner`
- `predicateLength * 4 != len(predicate)`
- `predicateDataLength != len(predicateData)`

If `h` is the block height the UTXO being spent was created, transaction is invalid if `blockheight() < h + maturity`.

> **Note:** when signing a transaction, `txPointer` is set to zero.
>
> **Note:** when verifying a predicate, `txPointer` is initialized to zero.
>
> **Note:** when executing a script, `txPointer` is initialized to zero.

The predicate root is computed identically to the contract root, used to compute the contract ID, [here](../id/contract.md).

## InputContract

| name          | type                         | description                                                             |
|---------------|------------------------------|-------------------------------------------------------------------------|
| `txID`        | `byte[32]`                   | Hash of transaction.                                                    |
| `outputIndex` | `uint8`                      | Index of transaction output.                                            |
| `balanceRoot` | `byte[32]`                   | Root of amount of coins owned by contract before transaction execution. |
| `stateRoot`   | `byte[32]`                   | State root of contract before transaction execution.                    |
| `txPointer`   | [TXPointer](./tx_pointer.md) | Points to the TX whose output is being spent.                           |
| `contractID`  | `byte[32]`                   | Contract ID.                                                            |

Transaction is invalid if:

- there is not exactly one output of type `OutputType.Contract` with `inputIndex` equal to this input's index

> **Note:** when signing a transaction, `txID`, `outputIndex`, `balanceRoot`, `stateRoot`, and `txPointer` are set to zero.
>
> **Note:** when verifying a predicate, `txID`, `outputIndex`, `balanceRoot`, `stateRoot`, and `txPointer` are initialized to zero.
>
> **Note:** when executing a script, `txID`, `outputIndex`, `balanceRoot`, and `stateRoot` are initialized to the transaction ID, output index, amount, and state root of the contract with ID `contractID`, and `txPointer` is initialized to zero.

## InputMessage

| name                  | type       | description                                                         |
|-----------------------|------------|---------------------------------------------------------------------|
| `messageID`           | `byte[32]` | The messageID as described [here](../id/utxo.md#message-id). |
| `sender`              | `byte[32]` | The address of the message sender.                                  |
| `recipient`           | `byte[32]` | The address or predicate root of the message recipient.             |
| `amount`              | `uint64`   | Amount of base asset coins sent with message.                       |
| `nonce`               | `uint64`   | The message nonce.                                                  |
| `witnessIndex`        | `uint8`    | Index of witness that authorizes spending the coin.                 |
| `dataLength`          | `uint16`   | Length of message data, in bytes.                                   |
| `predicateLength`     | `uint16`   | Length of predicate, in instructions.                               |
| `predicateDataLength` | `uint16`   | Length of predicate input data, in bytes.                           |
| `data`                | `byte[]`   | The message data.                                                   |
| `predicate`           | `byte[]`   | Predicate bytecode.                                                 |
| `predicateData`       | `byte[]`   | Predicate input data (parameters).                                  |

Given helper `len()` that returns the number of bytes of a field.

Transaction is invalid if:

- `witnessIndex >= tx.witnessesCount`
- `dataLength > MAX_MESSAGE_DATA_LENGTH`
- `predicateLength > MAX_PREDICATE_LENGTH`
- `predicateDataLength > MAX_PREDICATE_DATA_LENGTH`
- If `predicateLength > 0`; the computed predicate root (see below) is not equal `owner`
- `dataLength != len(data)`
- `predicateLength * 4 != len(predicate)`
- `predicateDataLength != len(predicateData)`

The predicate root is computed identically to the contract root, used to compute the contract ID, [here](../../identifiers.md#contract-id).
