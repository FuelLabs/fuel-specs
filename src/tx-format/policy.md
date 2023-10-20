# Policy

```c++
// index using powers of 2 for efficient bitmasking
enum PolicyType : uint32 {
    GasPrice = 1,
    GasLimit = 2,
    WitnessLimit = 4,
    Maturity = 8,
    MaxFee = 16,
}
```

| name   | type                                                                                                         | description     |
|--------|--------------------------------------------------------------------------------------------------------------|-----------------|
| `data` | One of [GasPrice](#gasprice), [GasLimit](#gaslimit), [WitnessLimit](#witnesslimit), or [Maturity](#maturity) | Policy data.    |

## GasPrice

| name       | type     | description               |
|------------|----------|---------------------------|
| `gasPrice` | `uint64` | Gas price for transaction |

## GasLimit

| name       | type     | description                                                |
|------------|----------|------------------------------------------------------------|
| `gasLimit` | `uint64` | Gas limit for transaction (applicable to script execution) |

Transaction is invalid if:

- `gasLimit > MAX_GAS_PER_TX`

## WitnessLimit

| name           | type     | description                                                    |
|----------------|----------|----------------------------------------------------------------|
| `witnessLimit` | `uint64` | The maximum amount of witness data allowed for the transaction |

Given helper `len()` that returns the number of bytes of a field.

Transaction is invalid if:

- `len(tx.witnesses) > witnessLimit`

## Maturity

| name       | type     | description                              |
|------------|----------|------------------------------------------|
| `maturity` | `uint32` | Block until which tx cannot be included. |

Transaction is invalid if:

- `blockheight() < maturity`

## MaxFee

| name      | type     | description                                  |
|-----------|----------|----------------------------------------------|
| `max_fee` | `uint64` | The maximum fee payable by this transaction. |

Transaction is invalid if:

- `max_fee > sum_inputs(tx, BASE_ASSET_ID) - sum_outputs(tx, BASE_ASSET_ID)`
- `max_fee < fee_balance(tx, BASE_ASSET_ID)`
