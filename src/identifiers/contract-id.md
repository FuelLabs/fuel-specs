# Contract ID
Here's a short and easy-to-understand explanation of the Contract ID calculation in points:

1. **Transaction Type**: Applies to `TransactionType.Create` transactions that create a new contract.

2. **Components**:
   - `0x4655454C`: Constant prefix ("FUEL" in hex).
   - `tx.data.salt`: Salt value for uniqueness.
   - `root(...)`: Merkle root of the bytecode Merkle tree.
   - `root_smt(...)`: Sparse Merkle Tree (SMT) root of the initial storage key-value pairs.

3. **Bytecode Merkle Tree**:
   - Bytecode divided into 16KiB chunks (leaves).
   - Final chunk zero-padded if not a multiple of 16KiB.
   - Merkle root calculated from the tree.

4. **Initial Storage SMT**:
   - Initial storage key-value pairs used to construct an SMT.
   - SMT root calculated.

5. **Contract ID Calculation**:
   - Contract ID = `sha256(0x4655454C ++ tx.data.salt ++ root(...) ++ root_smt(...))`.
   - Concatenation of the prefix, salt, bytecode Merkle root, and storage SMT root.
   - Hashed using SHA-256 to produce the unique contract ID.

The contract ID is a deterministic and unique identifier for each contract deployment, incorporating the bytecode, initial storage, and a salt value.
