# Output

```c++
enum OutputType : uint8 {
    Coin = 0,
    Contract = 1,
    Change = 2,
    Variable = 3,
    ContractCreated = 4,
}
```

| name   | type                                                                                                                                                                                       | description     |
|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|
| `type` | [`OutputType`](#output)                                                                                                                                                                      | Type of output. |
| `data` | One of [`OutputCoin`](#outputcoin), [`OutputContract`](#outputcontract), [`OutputChange`](#outputchange), [`OutputVariable`](#outputvariable), or [`OutputContractCreated`](#outputcontractcreated). | Output data.    |

Transaction is invalid if:

- `type > OutputType.ContractCreated`

## `OutputCoin`

| name       | type       | description                          |
|------------|------------|--------------------------------------|
| `to`       | `byte[32]` | Receiving address or predicate root. |
| `amount`   | `uint64`   | Amount of coins to send.             |
| `asset_id` | `byte[32]` | Asset ID of coins.                   |

## `OutputContract`

| name          | type       | description                                                            |
|---------------|------------|------------------------------------------------------------------------|
| `inputIndex`  | `uint16`    | Index of input contract.                                               |
| `balanceRoot` | `byte[32]` | Root of amount of coins owned by contract after transaction execution. |
| `stateRoot`   | `byte[32]` | State root of contract after transaction execution.                    |

Transaction is invalid if:

- `inputIndex >= tx.inputsCount`
- `tx.inputs[inputIndex].type != InputType.Contract`

> **Note:** when signing a transaction, `balanceRoot` and `stateRoot` are set to zero.
>
> **Note:** when verifying a predicate or executing a script, `balanceRoot` and `stateRoot` are initialized to zero.

The balance root `balanceRoot` is the root of the [SMT](../protocol/cryptographic-primitives.md#sparse-merkle-tree) of balance leaves. Each balance is a `uint64`, keyed by asset ID (a `byte[32]`).

The state root `stateRoot` is the root of the [SMT](../protocol/cryptographic-primitives.md#sparse-merkle-tree) of storage slots. Each storage slot is a `byte[32]`, keyed by a `byte[32]`.

## `OutputChange`

| name       | type       | description                          |
|------------|------------|--------------------------------------|
| `to`       | `byte[32]` | Receiving address or predicate root. |
| `amount`   | `uint64`   | Amount of coins to send.             |
| `asset_id` | `byte[32]` | Asset ID of coins.                   |

Transaction is invalid if:

- any other output has type `OutputType.OutputChange` and asset ID `asset_id` (i.e. only one change output per asset ID is allowed)

> **Note:** when signing a transaction, `amount` is set to zero.
>
> **Note:** when verifying a predicate or executing a script, `amount` is initialized to zero.

This output type indicates that the output's amount may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

## `OutputVariable`

| name       | type       | description                          |
|------------|------------|--------------------------------------|
| `to`       | `byte[32]` | Receiving address or predicate root. |
| `amount`   | `uint64`   | Amount of coins to send.             |
| `asset_id` | `byte[32]` | Asset ID of coins.                   |

> **Note:** when signing a transaction, `to`, `amount`, and `asset_id` are set to zero.
>
> **Note:** when verifying a predicate or executing a script, `to`, `amount`, and `asset_id` are initialized to zero.

This output type indicates that the output's amount and owner may vary based on transaction execution, but is otherwise identical to a [Coin](#outputcoin) output. An `amount` of zero after transaction execution indicates that the output is unspendable and can be pruned from the UTXO set.

## `OutputContractCreated`

| name         | type       | description                     |
|--------------|------------|---------------------------------|
| `contractID` | `byte[32]` | Contract ID.                    |
| `stateRoot`  | `byte[32]` | Initial state root of contract. |
