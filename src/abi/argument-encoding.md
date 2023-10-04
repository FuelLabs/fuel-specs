# Argument Encoding

When crafting transaction script data, you must encode the arguments you wish to pass to the script.

## Types

These are the available types that can be encoded in the ABI:

- Unsigned integers:
  - `u8`, 8 bits.
  - `u16`, 16 bits.
  - `u32`, 32 bits.
  - `u64`, 64 bits.
  - `u128`, 128 bits.
  - `u256`, 256 bits.
- Boolean: `bool`, either `0` or `1` encoded identically to `u8`.
- B256: `b256`, arbitrary 256-bits value.
- Address : `address`, a 256-bit (32-byte) address.
- Fixed size string
- Array
- Enums (sum types)
- Structs
- Vectors
- Tuples

These types are encoded in-place. Here's how to encode them. We define `enc(X)` the encoding of the type `X`.

## Unsigned Integers

`u<M>` where `M` is either 8, 16, 32, 64, 128 or 256 bits.

`enc(X)` is the big-endian (i.e. right-aligned) representation of `X` left-padded with zero-bytes.
- In the case of `M` being 8, 16, 32 or 64, total length must be 8 bytes.
- If `M` is 128, total length must be 16 bytes.
- If `M` is 256, total length must be 32 bytes.

> **Note:** since all integer values are unsigned, there is no need to preserve the sign when extending/padding; padding with only zeroes is sufficient._

**Example:**

Encoding `42` yields: `0x000000000000002a`, which is the hexadecimal representation of the decimal number `42`, right-aligned to 8 bytes.
Encoding `u128::MAX - 1` yields: `0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFE`, it's right-alined to 16 bytes

## Boolean

`enc(X)` is `0` if `X` is false or `1` if `X` is true, left-padded with zero-bytes. Total length must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `true` yields:

```text
0x0000000000000001
```

## B256

`b256` is a fixed size bit array of length 256. Used for 256-bit hash digests and other 256-bit types. It is encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

## Address

A 256-bit (32-byte) address, encoded in the same way as a `b256` argument: encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

## Array

An array of a certain type `T`, `[T; n]`, where `n` is the length of the array.

Arrays in Sway have a fixed-length which is known at compile time. This means the ABI encoding for arrays also happens in-place, with no need to account for dynamic sizing.

The encoding for the array contains, in order, the encoding of each element in `[T; n]`, recursively following the encoding for the type `T`.

For instance, consider the function signature `my_func(bool, [u64; 2])` with the values `(true, [1, 2])`.

The encoding will be:

1. `0x0000000000000001`, the `true` bool encoded in-place.
2. `0x0000000000000001`, First element of the parameter `[u64; 2]`, `1`, encoded as a `u64`.
3. `0x0000000000000002`, Second element of the parameter `[u64; 2]`, `2`, encoded as a `u64`.

The resulting encoded ABI will be:

```text
0x000000000000000100000000000000010000000000000002
```

## Fixed-length Strings

Strings have fixed length and are encoded in-place. `str[n]`, where `n` is the fixed-size of the string. Rather than padding the string, the encoding of the elements of the string is tightly packed. And unlike the other type encodings, the string encoding is left-aligned.

Note that all strings are encoded in UTF-8.

**Example:**

Encoding `"Hello, World"` as a `str[12]` **yields**:

```text
0x48656c6c6f2c20576f726c6400000000
```

Note that we're padding with zeroes in order to keep it right-aligned to 8 bytes (FuelVM's word size).

## Structs

Encoding ABIs that contain custom types, such as structs, is similar to encoding a set of primitive types. The encoding will proceed in the order that the inner types of a custom type are declared and _recursively_ just like encoding any other type. This way you can encode structs with primitive types or structs with more complex types in them such as other structs, arrays, strings, and enums.

Here's an example:

```rust
struct InputStruct {
    field_1: bool,
    field_2: u8,
}


abi MyContract {
    fn foo(a: u64);
    fn bar(a: InputStruct);
} {
    fn baz(a: ()) { }
}
```

Calling `bar` with `InputStruct { field_1: true, field_2: 5 }` yields:

```plaintext
0x
0000000000000001 // `true` encoded as a bool
0000000000000005 // `5` encoded as u8
```

A more complex scenario:

```rust
struct InputStruct {
    field_1: bool,
    field_2: [u8; 2], // An array of u8, with length 2.
}


abi MyContract {
    fn foo(a: u64);
    fn bar(a: InputStruct);
} {
    fn baz(a: ()) { }
}
```

Calling `bar` with `InputStruct { field_1: true, field_2: [1, 2] }` yields:

```plaintext
0x
0000000000000001 // `true` encoded as a bool
0000000000000001 // `1` encoded as u8
0000000000000002 // `2` encoded as u8
```

## Enums (sum types)

ABI calls containing enums (sum types) are encoded similarly to structs: encode the discriminant first, then recursively encode the type of the enum variant being passed to the function being called.

```rust
enum MySumType
{
    X: u32,
    Y: bool,
}

abi MyContract {
    fn foo(a: u64);
    fn bar(a: MySumType);
} {
    fn baz(a: ()) { }
}
```

Calling `bar` with `MySumType::X(42)` yields:

```plaintext
0x
0000000000000000 // The discriminant of the chosen enum, in this case `0`.
000000000000002a // `42` encoded as u64
```

If the sum type has variants of different sizes, then left padding is required.

```rust
enum MySumType
{
    X: b256,
    Y: u32,
}

abi MyContract {
    fn foo(a: u64);
    fn bar(a: MySumType);
} {
    fn baz(a: ()) { }
}
```

Calling `bar` with `MySumType::Y(42)` yields:

```plaintext
0x
0000000000000001 // The discriminant of the chosen enum, in this case `1`.
0000000000000000 // Left padding
0000000000000000 // Left padding
0000000000000000 // Left padding
000000000000002a // `42` encoded as u64
```

Note that three words of padding are required because the largest variant of `MySumType` is 4 words wide.

If all the variants of a sum type are of type `()`, or unit, then only the discriminant needs to be encoded.

```rust
enum MySumType
{
    X: (),
    Y: (),
    Z: (),
}

abi MyContract {
    fn foo(a: u64);
    fn bar(a: MySumType);
} {
    fn baz(a: ()) { }
}
```

Calling `bar` with `MySumType::Z` yields:

```plaintext
0x
0000000000000002 // The discriminant of the chosen enum, in this case `2`.
```

## Vectors

ABI calls containing vectors are encoded in the following way:

- First, figure out the the length `l` of the vector. Its length will also be its capacity.
- Encode the content of the vector according to the spec of its type, e.g. for a `Vec<bool>`,
  encode each `bool` element according to the `bool` specs. This gives out data that is stored
  on the heap, and we encode only the pointer to this data

```rust
abi MyContract {
  fn foo(a: Vec<u32>);
} {
  fn foo(a: Vec < u32 > ) {};
}
```

Calling `foo` with `vec![1u32, 2u32, 3u32, 4u32]`:

- `length` is 4, `capacity` is 4
- `data`: [0x0000000000000001, 0x0000000000000002, 0x0000000000000003, 0x0000000000000004], stored at pointer address `0x000000000000beef`

> Note: to understand how the `u32` are encoded, see the section about encoding integers.

```plaintext
0x
000000000000beef // pointer address
0000000000000004 // length = 4
0000000000000004 // capacity = 4
```

At the pointer address, then the vector's content are encoded as such:

```plaintext
0x
0000000000000001 // 1u32
0000000000000002 // 2u32
0000000000000003 // 3u32
0000000000000004 // 4u32
```

# Tuples

ABI calls containing tuples are encoded as such:
If `X` is a tuple with the type signature `(T_1, T_2, ..., T_n)`, with `T_1,...,T_n` being any type except for vector, then `enc(X)` is encoded as the concatenation of `enc(T_1)`, `enc(T_2)`,`enc (T_3)`, ..., `enc(T_n)`.


```rust
abi MyContract {
  fn foo(a: (u64, str[3], bool));
} {
  fn foo(a: (u64, str[3], bool)) {};
}
```

Calling `foo` with `(1u64, "hel", true)` :

```plaintext
0x
0000000000000001 // 1u64
68656c0000000000 // "hel" encoded as per the specs
0000000000000001 // true
```
