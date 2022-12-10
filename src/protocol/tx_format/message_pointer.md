# MessagePointer

The location of the message on the base chain. It can be used by UTXOs as a reference to the message or by the transaction itself to make it unique.

| name          | type     | description        |
|---------------|----------|--------------------|
| `blockHeight` | `uint32` | Block height.      |
| `txIndex`     | `uint16` | Transaction index. |
| `logIndex`    | `uint16` | Log index.         |
