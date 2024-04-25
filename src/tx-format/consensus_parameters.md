# Consensus Parameters

| name                        | type      | description                                                    |
|-----------------------------|-----------|----------------------------------------------------------------|
| `GAS_PER_BYTE`              | `uint64`  | Gas charged per byte of the transaction.                       |
| `GAS_PRICE_FACTOR`          | `uint64`  | Unit factor for gas price.                                     |
| `MAX_GAS_PER_TX`            | `uint64`  | Maximum gas per transaction.                                   |
| `MAX_INPUTS`                | `uint64`  | Maximum number of inputs.                                      |
| `MAX_OUTPUTS`               | `uint64`  | Maximum number of outputs.                                     |
| `MAX_PREDICATE_LENGTH`      | `uint64`  | Maximum length of predicate, in instructions.                  |
| `MAX_GAS_PER_PREDICATE`     | `uint64`  | Maximum gas per predicate.                                     |
| `MAX_PREDICATE_DATA_LENGTH` | `uint64`  | Maximum length of predicate data, in bytes.                    |
| `MAX_SCRIPT_LENGTH`         | `uint64`  | Maximum length of script, in instructions.                     |
| `MAX_SCRIPT_DATA_LENGTH`    | `uint64`  | Maximum length of script data, in bytes.                       |
| `MAX_MESSAGE_DATA_LENGTH`   | `uint64`  | Maximum length of message data, in bytes.                      |
| `MAX_STORAGE_SLOTS`         | `uint64`  | Maximum number of initial storage slots.                       |
| `MAX_TRANSACTION_SIZE`      | `uint64`  | Maximum size of a transaction, in bytes.                       |
| `MAX_WITNESSES`             | `uint64`  | Maximum number of witnesses.                                   |
| `MAX_BYTECODE_SUBSECTIONS`  | `uint64`  | Maximum number of bytecode subsections.                        |
| `CHAIN_ID`                  | `uint64`  | A unique per-chain identifier.                                 |
| `BASE_ASSET_ID`             | `bytes32` | The base asset of the chain.                                   |
| `PRIVILEGED_ADDRESS`        | `bytes32` | The privileged address of the network who can perform upgrade. |
