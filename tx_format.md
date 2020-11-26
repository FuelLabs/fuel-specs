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

| name             | type                    | description                |
| ---------------- | ----------------------- | -------------------------- |
| `scriptLength`   | `uint16`                | Script length, in words.   |
| `script`         | `byte[]`                | Script to execute.         |
| `gasPrice`       | `uint64`                | Gas price for transaction. |
| `gasLimit`       | `uint64`                | Gas limit for transaction. |
| `inputsCount`    | `uint8`                 | Number of inputs.          |
| `inputs`         | [Input](#input)`[]`     | List of inputs.            |
| `outputsCount`   | `uint8`                 | Number of outputs.         |
| `outputs`        | [Output](#output)`[]`   | List of outputs.           |
| `witnessesCount` | `uint8`                 | Number of witnesses.       |
| `witnesses`      | [Witness](#witness)`[]` | List of witneses.          |

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

| name           | type       | description                                         |
| -------------- | ---------- | --------------------------------------------------- |
| `utxoID`       | `byte[32]` | UTXO ID.                                            |
| `dataLength`   | `uint16`   | Length of data, in bytes.                           |
| `data`         | `byte[]`   | Data to input into script.                          |
| `witnessIndex` | `uint8`    | Index of witness that authorizes spending the coin. |

### InputContract

| name         | type       | description  |
| ------------ | ---------- | ------------ |
| `utxoID`     | `byte[32]` | UTXO ID.     |
| `contractID` | `byte[32]` | Contract ID. |

## Output

```
enum  OutputType : uint8 {
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

| name                 | type    | description                                             |
| -------------------- | ------- | ------------------------------------------------------- |
| `inputIndex`         | `uint8` | Index of input contract.                                |
| `amountWitnessIndex` | `uint8` | Index of witness for amount of coins owned by contract. |

## Witness

| name         | type     | description                       |
| ------------ | -------- | --------------------------------- |
| `dataLength` | `uint16` | Length of witness data, in bytes. |
| `data`       | `byte[]` | Witness data.                     |
