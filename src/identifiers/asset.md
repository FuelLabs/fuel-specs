# Asset ID

The _asset ID_ (also called _asset hash_) of a asset is computed as
the [hash](../protocol/cryptographic-primitives.md#hashing) of the `CONTRACT_ID` and a 256-bit `SUB_IDENTIFIER`.

```python
sha256(CONTRACT_ID ++ SUB_IDENTIFIER)
```
