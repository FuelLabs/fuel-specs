# Predicate ID

For an input of type `InputType.Coin` or `InputType.Message`, `input`, the predicate owner is calculated as:
`sha256(0x4655454C ++ CHAIN_ID ++ root(input.predicate))`, where `root` is the Merkle root of
[the binary Merkle tree](../cryptographic_primitives.md#binary-merkle-tree) with each leaf being an 8-byte word of
two instructions. If the bytecode is not a multiple of 8 bytes (i.e. if there are an odd number of instructions),
the last instruction is padded with 4-byte zero.
