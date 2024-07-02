# Hash based IDs

Hash based ids are deterministically generated from associated types and are used in the JSON ABI for `type` IDs and for `logId`.
This document specifies how the hash based IDS are generated for `type` IDs and for `logId`.

## Generation

Hash based ids for `type` IDs are generated from the `sha256` of a string that represents the type.

For `logIds` we use the first 8 bytes of the `sha256` of a string that represents the type, this is because the `LOG` and `LOGD` opcodes use a 64bit value as log id.

## String representation of types

For describing the string representation of type we will use the notation `{abi_str(T)}` that should be replaced by the respective ABI string representation of the respective type `T`.

### Intrinsics

 `u8`   => `"u8"`
 `u16`  => `"u16"`
 `u32`  => `"u32"`
 `u64`  => `"u64"`
 `u256` => `"u256"`
 `b256` => `"b256"`
 `bool` => `"bool"`

### String arrays

  String array of size `1` => `"str[1]"`
  String array of size `2` => `"str[2]"`
  etc.

### String slices

  String slice => `"str"`

### Arrays

 `[T; 1]` => `"[{abi_str(T)}; 1]"`
 `[T; 2]` => `"[{abi_str(T)}; 2]"`
etc.

### Tuples

 `()`      => `"()"`
 `(T1)`    => `"({abi_str(T1)})"`
 `(T1,T2)` => `"({abi_str(T1)}, {abi_str(T2)})"`
etc.

### Enums

  `Option` enum with type parameter `T` => `"enum std::option::Option<{abi_str(T)}>"`
  Enum without type parameters named `MyEnum` => `"enum MyEnum"`
  Enum with type parameter `T1` named `MyEnum` => `"enum MyEnum<{abi_str(T1)}>"`
  Enum with type parameters `T1`, `T2` named `MyEnum` in `my_module` => `"enum my_module::MyEnum<{abi_str(T1)},{abi_str(T2)}>"`

### Structs

  `Vec` struct with type parameter `T` => `"struct std::vec::Vec<{abi_str(T)}>"`
  Struct without type parameters named `MyStruct` => `"struct MyStruct"`
  Struct with type parameter `T1` named `MyStruct` => `"struct MyStruct<{abi_str(T1)}>"`
  Struct with type parameters `T1`, `T2` named `MyStruct` in `my_module` => `"struct my_module::MyStruct<{abi_str(T1)},{abi_str(T2)}>"`

### Generic Type Parameter

 Generic type parameter `T` if root type => `"generic T"`
 Generic type parameter `T` if not root type => `"T"` as in `"struct MyStruct<T>"`

### Complex examples composition

 Tuple of array and `u64` => `"([u64,1]; u64)"`
 Array of `Option<u64>`=> `"[enum std::option::Option<u64>; 3]"`
 Struct with tuple type parameter => `"struct my_module::MyStruct<(u64, u64)>"`
