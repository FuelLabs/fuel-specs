# Predicate ID

For an input of type `InputType.Coin` or `InputType.Message`, `input`, the predicate owner is calculated as:
`sha256(0x4655454C ++ root(input.predicate))`, where `root` is the Merkle root of
[the binary Merkle tree](../protocol/cryptographic-primitives.md#binary-merkle-tree) each leaf being 16KiB of instructions.
If the bytecode is not a multiple of 16 KiB, the final leaf should be zero-padded rounding up to the nearest multiple of 8 bytes.
