# Policy

```c++
// index using powers of 2 for efficient bitmasking
enum PolicyType : uint32 {
    Tip = 1,
    WitnessLimit = 2,
    Maturity = 4,
    MaxFee = 8,
}
```

| name   | type                                                                              | description  |
|--------|-----------------------------------------------------------------------------------|--------------|
| `data` | One of [`Tip`](#tip), [`WitnessLimit`](#witnesslimit), or [`Maturity`](#maturity) | Policy data. |

## `Tip`

| name       | type     | description                                                                                   |
|------------|----------|-----------------------------------------------------------------------------------------------|
| `tip` | `uint64` | Additional, optional fee in `BASE_ASSET` to incentivize block producer to include transaction |

## `WitnessLimit`

| name           | type     | description                                                    |
|----------------|----------|----------------------------------------------------------------|
| `witnessLimit` | `uint64` | The maximum amount of witness data allowed for the transaction |

Given helper `len()` that returns the number of bytes of a field.

Transaction is invalid if:

- `len(tx.witnesses) > witnessLimit`

## `Maturity`

| name       | type     | description                              |
|------------|----------|------------------------------------------|
| `maturity` | `uint32` | Block until which the transaction cannot be included. |

Transaction is invalid if:

- `blockheight() < maturity`

## `MaxFee`

| name      | type     | description                                                                                                                                                           |
|-----------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `max_fee` | `uint64` | Required policy to specify the maximum fee payable by this transaction using `BASE_ASSET`. This is used to check transactions before the actual `gas_price` is known. |

Transaction is invalid if:

- `max_fee > sum_inputs(tx, BASE_ASSET_ID) - sum_outputs(tx, BASE_ASSET_ID)`
- `max_fee < max_fee(tx, BASE_ASSET_ID, gas_price)`
