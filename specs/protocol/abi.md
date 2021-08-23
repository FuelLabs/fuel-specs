# Contract ABI Format

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

### Receipt

Upon execution of ABI calls, i.e scripts being executed, a JSON representing a list of receipts will be returned to the caller. Below is the JSON specification of the many receipt types. The root will be `receipts_root` which will include an array of `receipts`.

```json
{
   "receipts_root":[
      {
         "type":"<receipt_type>",
         ...
      },
      ...
   ]
}
```

All receipts will have a `type` property:

- `type`: String, type of the receipt. Can be one of:
  - Call
  - Return
  - ReturnData
  - Panic
  - Revert
  - Log
  - LogData
  - Transfer
  - TransferOut

Then, each receipt type will have its own properties. Some of these properties are related to the Virtual Machine's registers, as specified in more details [here](https://github.com/FuelLabs/fuel-specs/blob/master/specs/vm/opcodes.md).

#### Panic receipt

- `type`: `Panic`.
- `id`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `reason`: Number, panic reason.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"Panic",
         "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "reason":1,
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### Return receipt

- `type`: `Return`.
- `id`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `val`: Number, value of register `$rA`.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"Return",
         "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "val": 18446744073709551613,
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### Call receipt

- `type`: `Call`.
- `from`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `to`: String representation of 32-Bytes,contract ID of called contract.
- `amount`: Number, amount of coins to forward.
- `color`: String representation of 32-Bytes, color of coins to forward.
- `gas`: Number, gas to forward, value in register `$rD`.
- `param1`: Number, first parameter.
- `param2`: Number, second parameter.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"Call",
         "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
         "amount": 10000,
         "color":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
         "gas": 500,
         "param1":184467440737095516,
         "param2":184467440737095516,
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### Log receipt

- `type`: `Log`.
- `id`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `val0`: Number, value of register `$rA`.
- `val1`: Number, value of register `$rB`.
- `val2`: Number, value of register `$rC`.
- `val3`: Number, value of register `$rD`.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"Log",
         "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "val0": 1844674407370,
         "val1": 1844674407371,
         "val2": 1844674407372,
         "val3": 1844674407373,
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### LogData receipt

- `type`: `LogData`.
- `id`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `val0`: Number, value of register `$rA`
- `val1`: Number, value of register `$rB`
- `ptr`: Number, value of register `$rC`.
- `len`: Number, value of register `$rD`.
- `digest`: String representation of 32-Bytes, hash of `MEM[$rC, $rD]`.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"LogData",
         "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "val0": 1844674407370,
         "val1": 1844674407371,
         "ptr": 1844674407372,
         "len": 1844,
         "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### ReturnData receipt

- `type`: `ReturnData`.
- `id`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `ptr`: Number, value of register `$rA`.
- `len`: Number, value of register `$rB`.
- `digest`: String representation of 32-Bytes, hash of `MEM[$rA, $rB]`.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"ReturnData",
         "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "ptr": 1844674407372,
         "len": 1844,
         "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### Revert receipt

- `type`: `Revert`.
- `id`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `val`: Number, value of register `$rA`.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"Revert",
         "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "val": 1844674407372,
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### Transfer receipt

- `type`: `Transfer`.
- `from`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `to`: String representation of 32-Bytes, _contract ID_ of contract to transfer coins to.
- `amount`: Number, amount of coins to forward.
- `color`: String representation of 32-Bytes, color of coins to forward.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"Transfer",
         "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
         "amount": 10000,
         "color":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

#### TransferOut receipt

- `type`: `TransferOut`.
- `from`: String representation of 32-Bytes, contract ID of current context if in an internal context, zero otherwise.
- `to`: String representation of 32-Bytes, _address_ to transfer coins to.
- `amount`: Number, amount of coins to forward.
- `color`: String representation of 32-Bytes, color of coins to forward.
- `pc`: Number, value of register `$pc`.
- `is`: Number, value of register `$is`.

```json
{
   "receipts_root":[
      {
         "type":"TransferOut",
         "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
         "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
         "amount": 10000,
         "color":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
         "pc":18446744073709551615,
         "is":18446744073709551614
      }
   ]
}
```

## Function Selector Encoding

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
0x000000000c36cb9c
```

## Argument Encoding

When crafting a transaction script data, you must encode the arguments you wish to pass to the script.

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
- Bytes32: `bytes32`, arbitrary 32-byte value.
- Address : `address`, a 32-byte address.
- Fixed size string
- Array
- Sum types
- Structs

### Static Encoding

Static types are encoded in-place. Here's how to encode these static types. We define `enc(X)` the encoding of the type `X`.

#### Unsigned Integers

`u<M>` where `M` is either 8, 16, 32, or 64 bits.

`enc(X)` is the big-endian representation of `X` left-padded with zero-bytes. Total length must be 8 bytes.

_Note: since all integer values are unsigned, there is no need to preserve the sign when extending/padding; padding with only zeroes is sufficient._

**Example:**

Encoding `42` yields: `0x000000000000002a`, which is the binary representation of the decimal number `42`, right-aligned to 8 bytes.

#### Boolean

`enc(X)` is `0` if `X` is false or `1` if `X` is true, left-padded with zero-bytes. Total length must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `true` yields:

```text
0x0000000000000001
```

#### Byte

`byte`, a single byte.

`enc(X)` is `X`, left-padded with zero-bytes. Total length, per argument, must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `255` yields:

```text
0x00000000000000ff
```

#### Bytes32

`bytes32` is a fixed size byte array of length 32. Used for 32-byte hash digests and other 32-byte types. It is encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

#### Address

A 32-byte address, encoded in the same way as a `Bytes32` argument: encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

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

In the second part, the second part of the array encoding starts, it contains, in order, the encoding of each element in `T[]`, recursively following the encoding for the type `T`.

Let's revisit the example from the section above:

`my_func(bool, str[])` with the values `(true, [1, 2])`.

The first part of the encoding will be:

1. `0x0000000000000001`, the `true` bool encoded in-place.
2. `0x0000000000000010`, the offset that points to where the data for the second parameter (`u8[]`) starts. In this case, `0x10 == 16`, which is exactly 2 words (16 bytes) after the beginning of the encoding.

Then, the second part of the encoding starts:

1. `0x0000000000000001`, `1` encoded as a u8, right-aligned to 8 bytes.
2. `0x0000000000000002`, `2` encoded as a u8, right-aligned to 8 bytes.

Note that the first value encoded in the second part starts at `0x10`, in other words: after 16 bytes. Which is exactly where the encoded second argument points to.

Also note that because Sway uses fixed-size arrays, the function signature will contain information about the length of any array parameters.

The resulting ABI will be:

```text
0x0000000000000001000000000000001000000000000000010000000000000002
```

#### Fixed-length Strings

Strings have fixed length and are encoded in the same way as an array. `string[n]`, where `n` is the fixed-size of the string.

Like an array, the first part of the encoding is the offset where the array data will start.

Then, in the data location, the encoding will contain the UTF-8 encoded string.

Note that all strings are encoded in UTF-8.

**Example:**

Encoding `"Hello, World"` as a `str[12]` **yields**:

```text
0x000000000000000848656c6c6f2c20576f726c64
```

Where `0x0000000000000008` is the offset and `0x48656c6c6f2c20576f726c64` is `"Hello, World"` encoded in UTF-8.

A more complex example is an array of strings, which is encoded like an array of arrays. Suppose the function `complex(string[])` with the parameters `["hello", "world"]`.

Let's start by encoding the most atomic arguments.

```text
1. a - offset for "hello"
2. b - offset for "world"
3. 0x00000068656c6c6f - encoding of "hello"
4. 0x000000776f726c64 - encoding of "world"
```

Now let's compute the `a` and `b` offsets.

The offset `a` should point to where the content for "hello" starts, which is line 3, which means we have to offset 2 lines (line 1 and line 2), so `2 * 8`, 16 bytes. `a = 0x0000000000000010`.

Same procedure for the offset `b`. `world` content starts at line 4. We have to offset 3 lines, `3 * 8`, 24 bytes. `a = 0x0000000000000018`.

So our final encoding will be:

`0x0000000000000010000000000000001800000068656c6c6f000000776f726c64`.

#### Structs

Encoding ABIs that contain custom types, such as structs, are similar to encoding arrays and strings: you must first encode the offset that points to where the data from the inner fields of the struct are going to be encoded at. At that location, the encoding will proceed _recursively_ just like encoding any other type, in other words: primitive types will be encoded in place and dynamic types will encode offsets. This way you can encode structs with primitive types or structs with more complex types in it, such as other structs, arrays, strings, and enums.

Here's an example:

```rust
struct InputStruct {
    field_1: bool,
    field_2: u8,
}


abi MyContract {
  fn foo(a: u64);
  fn bar(a: InputStruct );
} {
  fn baz(a: ()) { }
}
```

Calling `bar` with `InputStruct{field_1: true, field_2: 5}` yields:

```plaintext
0x
0000000000000008 // Offset to InputStruct (exactly one word)
0000000000000001 // `true` encoded as a bool
0000000000000005 // `5` encoded as u8
```

More complex scenario

```rust
struct InputStruct {
    field_1: bool,
    field_2: u8[], // An array of u8
}


abi MyContract {
  fn foo(a: u64);
  fn bar(a: InputStruct );
} {
  fn baz(a: ()) { }
}
```

Calling `bar` with `InputStruct{field_1: true, field_2: [1,2]}` yields:

```plaintext
0x
0000000000000008 // Offset to InputStruct (exactly one word)
0000000000000001 // `true` encoded as a bool
0000000000000008 // Another offset to `field_2` which is an array (dynamic encoding)
0000000000000001 // `1` encoded as u8
0000000000000002 // `2` encoded as u8
```

#### Sum types (Enums)

ABI calls containing sum types (enums) are encoded identically to structs: encode the offset first, then recursively encode the type of the enum variant being passed to the function being called.

```rust
enum  MySumType
{
    x: u32,
    y: bool,
}

abi MyContract {
  fn foo(a: u64);
  fn bar(a: MySumType );
} {
  fn baz(a: ()) { }
}
```

Calling `bar` with `MySumType::x(42)` yields:

```plaintext
0x
0000000000000008 // Offset to MySumType (exactly one word)
000000000000002a // `42` encoded as u64
```

TODO: I'm assuming the encoder/decoder can infer which variant is being passed by the call arguments (e.g `MySumType::x(42)` being passed to the call, so it knows it's the variant `x`). We could also encode the enum variant's numeric representation (e.g 0, 1, 2, ...) in the ABI. Thoughts?
