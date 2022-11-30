# JSON Format for Contract Storage Initializers

Contracts can request that certain storage slots are initialized to specific values. These initialized slots are represented in JSON format as an array where each element represents a storage slot and has the following properties:

- `"key"`: String, a 32-byte key for a given storage slot;
- `"value"`: String, a 32-byte value that initializes the slot;

For instance, the following is a JSON object that requests that the 3 storage slots with keys `0x11..11`, `0x22..22`, and `0x33..33`, are respectively initialized to the values indicated.

```json
[
  {
    "key": "0x1111111111111111111111111111111111111111111111111111111111111111",
    "value": "0x1010101010101010101010101010101010101010101010101010101010101010"
  }, 
  {
    "key": "0x2222222222222222222222222222222222222222222222222222222222222222",
    "value": "0x2020202020202020202020202020202020202020202020202020202020202020"
  },
  {
    "key": "0x3333333333333333333333333333333333333333333333333333333333333333",
    "value": "0x0303030303030303030303030303030303030303030303030303030303030303"
  },
]
```
