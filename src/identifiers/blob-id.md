# Blob ID

The _blob ID_ (also called _blob hash_) of a transaction is computed as
the [hash](../protocol/cryptographic-primitives.md#hashing) of the blob data.

Blob ID calculation doesn't vary between chains.

```python
sha256(blob_data)
```
