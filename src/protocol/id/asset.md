# Asset ID

The _asset ID_ (also called _asset hash_) of a asset is computed as
the [hash](../cryptographic_primitives.md#hashing) of the `CONTRACT_ID` and a 256-bit `IDENTIFIER`.

```python
sha256(CONTRACT_ID ++ IDENTIFIER)
```
