# Predicate ID
Here's a shorter explanation of the Predicate ID calculation in points:

1. **Input Types**: Applies to inputs of type `InputType.Coin` or `InputType.Message`.

2. **Components**:
   - `0x4655454C`: Constant prefix ("FUEL" in hex).
   - `root(input.predicate)`: Merkle root of the predicate bytecode Merkle tree.

3. **Predicate Bytecode Merkle Tree**:
   - Predicate bytecode divided into 16KiB chunks (leaves).
   - Final chunk zero-padded if not a multiple of 16KiB.
   - Merkle root calculated from the tree.

4. **Predicate ID Calculation**:
   - Predicate ID = `sha256(0x4655454C ++ root(input.predicate))`.
   - Concatenation of the prefix and the predicate bytecode Merkle root.
   - Hashed using SHA-256 to produce the unique Predicate ID.

The Predicate ID is a deterministic and unique identifier for the predicate bytecode associated with a coin or message input. It is computed by constructing a Merkle tree from the predicate bytecode and hashing its root along with a constant prefix.
