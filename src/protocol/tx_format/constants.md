# Constants

| name                        | type     | value           | description                                   |
|-----------------------------|----------|-----------------|-----------------------------------------------|
| `GAS_PER_BYTE`              | `uint64` |                 | Gas charged per byte of the transaction.      |
| `GAS_PRICE_FACTOR`          | `uint64` | `1,000,000,000` | Unit factor for gas price.                    |
| `MAX_GAS_PER_TX`            | `uint64` |                 | Maximum gas per transaction.                  |
| `MAX_INPUTS`                | `uint64` | `8`             | Maximum number of inputs.                     |
| `MAX_OUTPUTS`               | `uint64` | `8`             | Maximum number of outputs.                    |
| `MAX_PREDICATE_LENGTH`      | `uint64` |                 | Maximum length of predicate, in instructions. |
| `MAX_PREDICATE_DATA_LENGTH` | `uint64` |                 | Maximum length of predicate data, in bytes.   |
| `MAX_SCRIPT_LENGTH`         | `uint64` |                 | Maximum length of script, in instructions.    |
| `MAX_SCRIPT_DATA_LENGTH`    | `uint64` |                 | Maximum length of script data, in bytes.      |
| `MAX_MESSAGE_DATA_LENGTH`   | `uint16` |                 | Maximum length of message data, in bytes.     |
| `MAX_STORAGE_SLOTS`         | `uint16` | `255`           | Maximum number of initial storage slots.      |
| `MAX_WITNESSES`             | `uint64` | `16`            | Maximum number of witnesses.                  |
| `MAX_NESTED_CALLS`          | `uint64` | `64`            | Maximum number of nested contract calls.      |
