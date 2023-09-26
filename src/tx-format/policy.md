# Policy

```c++
enum PolicyType : uint8 {
    GasLimit = 0,
    WitnessLimit = 1,
    Maturity = 2,
}
```

| name   | type                                                                                  | description    |
|--------|---------------------------------------------------------------------------------------|----------------|
| `type` | [PolicyType](#policy)                                                                 | Type of input. |
| `data` | One of [GasLimit](#gaslimit), [WitnessLimit](#witnesslimit), or [Maturity](#maturity) | Input data.    |

Transaction is invalid if:

- `type > PolicyType.Maturity`

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

- `witnessLimit > len(tx.witnesses)`

## Maturity

| name       | type     | description                              |
|------------|----------|------------------------------------------|
| `maturity` | `uint32` | Block until which tx cannot be included. |

Transaction is invalid if:

- `blockheight() < maturity`
