# Policy

```c++
// index using power of 2's for efficient bitmasking
enum PolicyType : uint32 {
    GasPrice = 1,
    GasLimit = 2,
    WitnessLimit = 4,
    Maturity = 8,
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
