# Contract ID

For a transaction of type `TransactionType.Create`, `tx`, the contract ID is
`sha256(0x4655454C ++ tx.data.salt ++ root(tx.data.witnesses[bytecodeWitnessIndex].data) ++ root_smt(tx.storageSlots))`,
where `root` is the Merkle root of [the binary Merkle tree](../protocol/cryptographic-primitives.md#binary-merkle-tree) with
each leaf being 16KiB of instructions, and `root_smt` is the
[Sparse Merkle tree](../protocol/cryptographic-primitives.md#sparse-merkle-tree) root of the provided key-value pairs.
If the bytecode is not a multiple of 16 KiB, the final leaf should be zero-padded rounding up to the nearest multiple
of 8 bytes.
