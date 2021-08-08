# FuelVM ABI Format

This document describes and specifies the ABI (Application Binary Interface) of the Fuel Virtual Machine, the Sway programming language, and contracts written in Sway.  

## JSON ABI Format

The JSON of an ABI is the human-readable representation of an interface call to a Sway contract. It is an array of functions containing the following properties:

- `type`: String, type of the function; right now only one is supported: `"function"`
- `name`: String, the name of the contract's function being called;
- `inputs`: An array of objects that represents the inputs to this function, each of which contains:
  - `name`: String, the name of the parameter;
  - `type`: String, the canonical Sway type of the parameter;
- `outputs`: An array of objects similar to `inputs`;

For instance:

```json
[
   {
      "type":"function",
      "inputs":[
         {
            "name":"arg",
            "type":"u64"
         }
      ],
      "name":"entry_one",
      "outputs":[
         
      ]
   }
]
```

This is a function called `entry_one` that takes one `u64` argument and has no returns.

This JSON should be both human-readable and parsable by the tooling around the FuelVM and the Sway programming language. There is a detailed specification for the binary encoding backing this readable descriptor. The section below specifies the encoding for the function being selected to be executed and each of the argument types.

## Function selector

To select which function you want to call, first, this function must be in an ABI struct of a Sway program. For instance:

```rust
abi MyContract {
  fn foo(a: u64);
  fn bar(a: InputStruct );
} {
  fn baz(a: ()) { }
}
```

The function selector is the first 4 bytes of the SHA-256 hash function of the signature of the Sway function being called. Then, these 4 bytes are right-aligned to 8 bytes, left-padded with zeroes.

_N.B.: the default word size for the Fuel Virtual Machine is 8 bytes._

The signature is composed of the function name with the parenthesized list of comma-separated parameter types without spaces. All strings encoded with UTF-8.

For instance, in the case of the function `entry_one` above, we would pass the string `"entry_one(u64)"` to the `sha256()` hashing algorithm, the full digest would be:

```text
0x0c36cb9cb766ff60422db243c4fff06d342949da3c64a3c6ac564941f84b6f06
```

Then we would get only the first 4 bytes of this digest and left pad it to 8 bytes:

```text
0x00000c36cb9c
```

## Argument encoding

When crafting a Sway script data, you must encode the arguments you wish to pass.  

**The encoding for the argument will depend on the type of the argument being encoded.**

### Types

These are the available types that can be encoded in the ABI:

- Unsigned integers:
  - `u8`, 8 bits.
  - `u16`, 16 bits.
  - `u32`, 32 bits.
  - `u64`, 64 bits.
- Boolean: `bool`, either `0` or `1` encoded identically to `u8`.
- Byte: `byte`, single byte.
- Bytes32: `bytes32`, 32-byte hash digest.
- Address : `address`, a 32-byte address.
- Fixed size string
- Array
- Sum type (TODO, dynamic)
- Structs (TODO, dynamic)

### Static Encoding

Static types are encoded in-place. Here's how to encode these static types. We define `enc(X)` the encoding of the type `X`.

#### Unsigned integers (u8, u16, u32, u64)

`u<M>` where `M` is either 8, 16, 32, or 64 bits.

`enc(X)` is the big-endian representation of `X` left-padded with zero-bytes. Total length, per argument, must be 8 bytes.

_Note: since all integer values are unsigned, there is no need to preserve the sign when extending/padding; padding with only zeroes is sufficient._

**Example:**

Encoding `42` yields: `0x00000002a`, which is the binary representation of the decimal number `42`, right-aligned to 8 bytes.

#### Boolean

`bool`, similar to a `u8`: `enc(X)` is the big-endian, right-aligned to 8 bytes, left-padded with zeroes. Total length, per argument, must be 8 bytes.

`1` equivalent to `true`, `0` equivalent to `false`.

**Example:**

Encoding `true` yields:

```text
0x00000001
```

#### Byte

`byte`, a single byte.

`enc(X)` is `X`, left-padded with zero-bytes. Total length, per argument, must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `255` yields:

```text
0x0000ffffffff
```

#### Bytes32

`bytes32` is a fixed size byte array of length 32. Used for 32-byte hash digests. It's encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` **yields**:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

#### Address

A 32-byte address, encoded in the same way as the `Bytes32` argument: encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` **yields**:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

### Dynamic Encoding

Encoding dynamic types, for instance, arrays, is slightly different. For these types we introduce a new section of the ABI: the dynamic data location, a place in the ABI reserved for the data stored in arguments with more complex types. For static types we don't need to make use of this section; the values are stored in place.

For instance, consider the function signature: `my_func(bool, u8[])` and that we're passing the following values: `(true, [1, 2])`.

The first part of the encoding will contain:

1. `true` encoded in-place, as it is plain static encoding
2. A pointer to where the array `u8[]` will start in the data location section of the ABI

Then, the second part will be the location section itself, containing the proper values for these complex types. The sections below specifies in details how to proper encode these types.

#### Array

An array of a certain type `T`, `T[]`. The first part of the array encoding will be the offset where its encoded data will be stored at. Once the first part of the ABI encoding is done (in-place values and pointers), the second part starts (data location).

In the second part, the second part of the array encoding starts, it contains, in order:

1. The length, in the bytes, of the array.
2. The encoding of each element in `T[]`, recursively following the encoding for the type `T`.

Let's revisit the example from the section above:

`my_func(bool, str[])` with the values `(true, [1, 2])`.

The first part of the encoding will be:

1. `0x00000001`, the `true` bool encoded in-place.
2. `0x000000010`, the offset that points to where the data for the second parameter (`u8[]`) starts. In this case, `0x10 == 16`, which is exactly 2 words (16 bytes) after the beginning of the encoding.

Then, the second part of the encoding starts, since we have a dynamic type:

1. `0x00000002`, the length of the array
2. `0x00000001`, `1` encoded as a u8, left-aligned to 8 bytes.
3. `0x00000002`, `2` encoded as a u8, left-aligned to 8 bytes.

Note that the first value encoded in the second part starts at `0x10`, in other words: after 16 bytes. Which is exactly where the encoded second argument points to.

The resulting ABI will be:

```text
0x00000001000000010000000020000000100000002
```

#### Fixed-length strings

Strings have fixed length and are encoded in the same way as an array. `string[n]`, where `n` is the fixed-size of the string.

Like an array, the first part of the encoding is the offset where the array data will start.

Then, in the data location, the encoding will contain:

1. The length of the string, in bytes, left-aligned to 8 bytes.
2. The UTF-8 encoded string.

It's encoded as its binary representation. Note that all strings are encoded in UTF-8.

**Example:**

Encoding `"Hello, World"` as a `str[12]` **yields**:

```text
0x000000080000000C48656c6c6f2c20576f726c64
```

Where `0x00000008` is the offset, `0x0000000C` (`12` in decimal) is the length of the string, and `0x48656c6c6f2c20576f726c64` is `"Hello, World"` encoded in UTF-8.

A more complex example is an array of strings, which is encoded like an array of arrays. Suppose the function `complex(string[])` with the parameters `["hello", "world"]`.

Let's start by encoding the most atomic arguments.

```text
1. a - offset for "hello"
2. b - offset for "world"
3. 0x00000005 - length of "hello"
4. 0x68656c6c6f - encoding of "hello"
5. 0x00000005 - length of "world"
6. 0776f726c64 - encoding of "world"
```

Now let's compute the `a` and `b` offsets.

The offset `a` should point to where the content for "hello" starts, which is line 3, which means we have to offset 2 lines (line 1 and line 2), so `2 * 8`, 16 bytes. `a = 0x000000010`.

Same procedure for the offset `b`. `world` content starts at line 5. We have to offset 4 lines, `4 * 8`, 32 bytes. `a = 0x000000020`.

So our final encoding will be:

`0x0000000100000000200000000568656c6c6f000000050776f726c64`.
