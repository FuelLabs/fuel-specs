# Compressed Transaction Format

This document specifies the _compressed_ transaction format, which is posted to a blockchain for data availability.

## Transaction

| name   | type                                                                                      | description       |
|--------|-------------------------------------------------------------------------------------------|-------------------|
| `type` | `TransactionType`                                                                         | Transaction type. |
| `data` | One of [TransactionScript](#transactionscript) or [TransactionCreate](#transactioncreate) | Transaction data. |

### TransactionScript

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
| `script`           | `byte[]`                | Script to execute.                       |
| `scriptData`       | `byte[]`                | Script input data (parameters).          |
| `inputs`           | [Input](#input)`[]`     | List of inputs.                          |
| `outputs`          | [Output](#output)`[]`   | List of outputs.                         |
| `witnesses`        | [Witness](#witness)`[]` | List of witnesses.                       |

### TransactionCreate

| name                   | type                    | description                                   |
|------------------------|-------------------------|-----------------------------------------------|
| `gasPrice`             | `uint64`                | Gas price for transaction.                    |
| `gasLimit`             | `uint64`                | Gas limit for transaction.                    |
| `maturity`             | `uint32`                | Block until which tx cannot be included.      |
| `bytecodeLength`       | `uint16`                | Contract bytecode length, in instructions.    |
| `bytecodeWitnessIndex` | `uint8`                 | Witness index of contract bytecode to create. |
| `staticContractsCount` | `uint8`                 | Number of static contracts.                   |
| `inputsCount`          | `uint8`                 | Number of inputs.                             |
| `outputsCount`         | `uint8`                 | Number of outputs.                            |
| `witnessesCount`       | `uint8`                 | Number of witnesses.                          |
| `salt`                 | `byte[32]`              | Salt.                                         |
| `staticContracts`      | `byte[32][]`            | List of static contracts.                     |
| `inputs`               | [Input](#input)`[]`     | List of inputs.                               |
| `outputs`              | [Output](#output)`[]`   | List of outputs.                              |
| `witnesses`            | [Witness](#witness)`[]` | List of witnesses.                            |

## TXOPointer

| name          | type     | description        |
|---------------|----------|--------------------|
| `blockHeight` | `uint32` | Block height.      |
| `txIndex`     | `uint16` | Transaction index. |

## AddressPointer

| name           | type     | description                 |
|----------------|----------|-----------------------------|
| `blockHeight`  | `uint32` | Block height.               |
| `addressIndex` | `uint16` | Address registration index. |

## Input

| name   | type                                                              | description    |
|--------|-------------------------------------------------------------------|----------------|
| `type` | `InputType`                                                       | Type of input. |
| `data` | One of [InputCoin](#inputcoin) or [InputContract](#inputcontract) | Input data.    |

### InputCoin

| name                  | type         | description                                                            |
|-----------------------|--------------|------------------------------------------------------------------------|
| `utxoPointer`         | `TXOPointer` | UTXO pointer.                                                          |
| `witnessIndex`        | `uint8`      | Index of witness that authorizes spending the coin.                    |
| `maturity`            | `uint32`     | UTXO being spent must have been created at least this many blocks ago. |
| `predicateLength`     | `uint16`     | Length of predicate, in instructions.                                  |
| `predicateDataLength` | `uint16`     | Length of predicate input data, in bytes.                              |
| `predicate`           | `byte[]`     | Predicate bytecode.                                                    |
| `predicateData`       | `byte[]`     | Predicate input data (parameters).                                     |

### InputContract

| name                | type         | description          |
|---------------------|--------------|----------------------|
| `contractIDPointer` | `TXOPointer` | Contract ID pointer. |

## Output

| name   | type                                                                                                                                                                                                                             | description     |
|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|
| `type` | `OutputType`                                                                                                                                                                                                                     | Type of output. |
| `data` | One of [OutputCoin](#outputcoin), [OutputContract](#outputcontract), [OutputWithdrawal](#outputwithdrawal) [OutputChange](#outputchange), [OutputVariable](#outputvariable), or [OutputContractCreated](#outputcontractcreated). | Output data.    |

### OutputCoin

| name         | type             | description                                       |
|--------------|------------------|---------------------------------------------------|
| `toPointer`  | `AddressPointer` | Receiving address pointer or script hash pointer. |
| `amount`     | `uint64`         | Amount of coins to send.                          |
| `colorIndex` | `uint32`         | Color index of coins.                             |

### OutputContract

| name         | type    | description              |
|--------------|---------|--------------------------|
| `inputIndex` | `uint8` | Index of input contract. |

### OutputWithdrawal

| name         | type             | description                  |
|--------------|------------------|------------------------------|
| `toPointer`  | `AddressPointer` | Receiving address pointer.   |
| `amount`     | `uint64`         | Amount of coins to withdraw. |
| `colorIndex` | `uint32`         | Color index of coins.        |

### OutputChange

| name         | type             | description                                       |
|--------------|------------------|---------------------------------------------------|
| `toPointer`  | `AddressPointer` | Receiving address pointer or script hash pointer. |
| `amount`     | `uint64`         | Amount of coins to send.                          |
| `colorIndex` | `uint32`         | Color index of coins.                             |

### OutputVariable

| name         | type             | description                                       |
|--------------|------------------|---------------------------------------------------|
| `toPointer`  | `AddressPointer` | Receiving address pointer or script hash pointer. |
| `amount`     | `uint64`         | Amount of coins to send.                          |
| `colorIndex` | `uint32`         | Color index of coins.                             |

### OutputContractCreated

| name         | type       | description  |
|--------------|------------|--------------|
| `contractID` | `byte[32]` | Contract ID. |

## Witness

| name         | type     | description                       |
|--------------|----------|-----------------------------------|
| `dataLength` | `uint16` | Length of witness data, in bytes. |
| `data`       | `byte[]` | Witness data.                     |
