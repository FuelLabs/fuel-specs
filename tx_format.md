# Transaction Format

- [Transaction](#transaction)
- [Input](#input)
    - [InputCoin](#inputcoin)
    - [InputContract](#inputcontract)
- [Output](#output)
    - [OutputCoin](#outputcoin)
    - [OutputContract](#outputcontract)
- [Witness](#witness)

## Transaction

| name             | type                    | description                      |
| ---------------- | ----------------------- | -------------------------------- |
| `version`        | `uint32`                | Transaction version. Always `0`. |
| `scriptLength`   | `uint32`                | Script length, in instructions.  |
| `gasPrice`       | `uint64`                | Gas price for transaction.       |
| `gasLimit`       | `uint64`                | Gas limit for transaction.       |
| `inputsCount`    | `uint32`                | Number of inputs.                |
| `outputsCount`   | `uint32`                | Number of outputs.               |
| `witnessesCount` | `uint32`                | Number of witnesses.             |
| `script`         | `byte[]`                | Script to execute.               |
| `inputs`         | [Input](#input)`[]`     | List of inputs.                  |
| `outputs`        | [Output](#output)`[]`   | List of outputs.                 |
| `witnesses`      | [Witness](#witness)`[]` | List of witnesses.               |

## Input

```
enum  InputType : uint32 {
    Coin = 0,
    Contract = 1,
}
```

| name   | type                                                              | description    |
| ------ | ----------------------------------------------------------------- | -------------- |
| `type` | `InputType`                                                       | Type of input. |
| `data` | One of [InputCoin](#inputcoin) or [InputContract](#inputcontract) | Input data.    |


### InputCoin

| name           | type       | description                                         |
| -------------- | ---------- | --------------------------------------------------- |
| `utxoID`       | `byte[32]` | UTXO ID.                                            |
| `dataLength`   | `uint32`   | Length of data, in bytes.                           |
| `witnessIndex` | `uint32`   | Index of witness that authorizes spending the coin. |
| `data`         | `byte[]`   | Data to input into script.                          |

### InputContract

| name         | type       | description  |
| ------------ | ---------- | ------------ |
| `utxoID`     | `byte[32]` | UTXO ID.     |
| `contractID` | `byte[32]` | Contract ID. |

## Output

```
enum  OutputType : uint32 {
    Coin = 0,
    Contract = 1,
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

### OutputContract

| name                 | type     | description                                             |
| -------------------- | -------- | ------------------------------------------------------- |
| `inputIndex`         | `uint32` | Index of input contract.                                |
| `amountWitnessIndex` | `uint32` | Index of witness for amount of coins owned by contract. |
| `stateWitnessIndex`  | `uint32` | Index of witness for state root of contract.            |

## Witness

| name         | type     | description                       |
| ------------ | -------- | --------------------------------- |
| `dataLength` | `uint32` | Length of witness data, in bytes. |
| `data`       | `byte[]` | Witness data.                     |
