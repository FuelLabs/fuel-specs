# `UpgradePurposeType`

```c++
enum UpgradePurposeType : uint8 {
    ConsensusParameters = 0,
    StateTransition = 1,
}
```

| name   | type                                                                                        | description              |
|--------|---------------------------------------------------------------------------------------------|--------------------------|
| `type` | [`UpgradePurposeType`](#upgradepurposetype)                                                 | Type of upgrade purpose. |
| `data` | One of [`ConsensusParameters`](#consensusparameters), [`StateTransition`](#statetransition) | Upgrade purposes.        |

Transaction is invalid if:

- `type` is not valid `UpgradePurposeType` value`

## `ConsensusParameters`

| name           | type       | description                                                                                                                   |
|----------------|------------|-------------------------------------------------------------------------------------------------------------------------------|
| `witnessIndex` | `uint16`   | Index of witness that contains a serialized(with [postcard](https://docs.rs/postcard/latest/postcard/)) consensus parameters. |
| `checksum`     | `byte[32]` | The hash of the serialized consensus parameters.                                                                              |

Given helper `deserialize_consensus_parameters()` that deserializes the consensus parameters from a witness by using [postcard](https://docs.rs/postcard/latest/postcard/) algorithm.

Transaction is invalid if:

- `witnessIndex >= tx.witnessesCount`
- `checksum != sha256(tx.data.witnesses[witnessIndex])`
- `deserialize_consensus_parameters(tx.data.witnesses[witnessIndex])` returns an error.

## `StateTransition`

| name           | type       | description                                                    |
|----------------|------------|----------------------------------------------------------------|
| `bytecodeRoot` | `byte[32]` | The root of the new bytecode of the state transition function. |
