# Block Header

This document specifies the on-chain block header format.

## BlockHeader

| name        | type      | description                                                                       |
|-------------|-----------|-----------------------------------------------------------------------------------|
| `height`    | `uint32`  | Fuel block height.                                                                |
| `number`    | `uint32`  | Ethereum block number.                                                            |
| `prevHash`  | `bytes32` | Block header hash of the previous block.                                          |
| `prevsRoot` | `bytes32` | [Merkle root](./cryptographic_primitives.md) of all previous block header hashes. |
| `txsRoot`   | `bytes32` | [Merkle root](./cryptographic_primitives.md) of transactions.                     |
