# Contract ABI Format

This document describes and specifies the ABI (Application Binary Interface) of the FuelVM, the Sway programming language, and contracts written in Sway.

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

This is a function called `entry_one` that takes one `u64` argument and does not return a value.

This JSON should be both human-readable and parsable by the tooling around the FuelVM and the Sway programming language. There is a detailed specification for the binary encoding backing this readable descriptor. The section below specifies the encoding for the function being selected to be executed and each of the argument types.

### Receipt

Upon execution of ABI calls, i.e scripts being executed, a JSON object representing a list of receipts will be returned to the caller. Below is the JSON specification of the possible receipt types. The root will be `receipts_root` which will include an array of `receipts`.

```json
{
   "receipts_list":[
      {
         "type":"<receipt_type>",
         ...
      },
      ...
   ]
}
```

All receipts will have a `type` property:

- `type`: String; the type of the receipt. Can be one of:
  - "Call"
  - "Return"
  - "ReturnData"
  - "Panic"
  - "Revert"
  - "Log"
  - "LogData"
  - "Transfer"
  - "TransferOut"

Then, each receipt type will have its own properties. Some of these properties are related to the FuelVM's registers, as specified in more detail [here](https://github.com/FuelLabs/fuel-specs/blob/master/specs/vm/opcodes.md).

_Important note:_ For the JSON representation of the receipts, we represent 64-bit unsigned integers as a JSON's `String` due to limitation around the type `Number` in the JSON specification, which only supports numbers up to `2^{53-1}`, whereas the FuelVM's registers hold values up to `2^64`.

#### Panic receipt

- `type`: `Panic`.
- `id`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `reason`: String representation of a 64-bit unsigned integer; panic reason.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Panic",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "reason":1,
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### Return receipt

- `type`: `Return`.
- `id`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context; `null` otherwise.
- `val`: String representation of a 64-bit unsigned integer; value of register `$rA`.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Return",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val":"18446744073709551613",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### Call receipt

- `type`: `Call`.
- `from`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context; `null` otherwise.
- `to`: Hexadecimal representation of the 256-bit (32 bytes) contract ID of the callee.
- `amount`: String representation of a 64-bit unsigned integer; amount of coins to forward.
- `color`: Hexadecimal string representation of the 256-bit (32 bytes) color of coins to forward.
- `gas`: String representation of a 64-bit unsigned integer; amount gas to forward; value in register `$rD`.
- `param1`: String representation of a 64-bit unsigned integer; first parameter, holds the function selector.
- `param2`: String representation of a 64-bit unsigned integer; second parameter.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Call",
    "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
    "amount":"10000",
    "color":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
    "gas":"500",
    "param1":"184467440737095516",
    "param2":"184467440737095516",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### Log receipt

- `type`: `Log`.
- `id`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `val0`: String representation of a 64-bit unsigned integer; value of register `$rA`.
- `val1`: String representation of a 64-bit unsigned integer; value of register `$rB`.
- `val2`: String representation of a 64-bit unsigned integer; value of register `$rC`.
- `val3`: String representation of a 64-bit unsigned integer; value of register `$rD`.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Log",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val0": "1844674407370",
    "val1": "1844674407371",
    "val2": "1844674407372",
    "val3": "1844674407373",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### LogData receipt

- `type`: `LogData`.
- `id`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `val0`: String representation of a 64-bit unsigned integer; value of register `$rA`
- `val1`: String representation of a 64-bit unsigned integer; value of register `$rB`
- `ptr`: String representation of a 64-bit unsigned integer; value of register `$rC`.
- `len`: String representation of a 64-bit unsigned integer; value of register `$rD`.
- `digest`: Hexadecimal string representation of the 256-bit (32 bytes) hash of `MEM[$rC, $rD]`.
- `data`: String representation of the value of the memory range `MEM[$rC, $rD]`.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"LogData",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val0":"1844674407370",
    "val1":"1844674407371",
    "ptr":"1844674407372",
    "len":"66544",
    "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### ReturnData receipt

- `type`: `ReturnData`.
- `id`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `ptr`: String representation of a 64-bit unsigned integer; value of register `$rA`.
- `len`: String representation of a 64-bit unsigned integer; value of register `$rB`.
- `digest`: String representation of 256-bit (32 bytes), hash of `MEM[$rA, $rB]`.
- `mem_range_value`: String representation of the value of the memory range `MEM[$rA, $rB]`.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"ReturnData",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "ptr":"1844674407372",
    "len":"1844",
    "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### Revert receipt

- `type`: `Revert`.
- `id`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `val`: String representation of a 64-bit unsigned integer; value of register `$rA`.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Revert",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val":"1844674407372",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### Transfer receipt

- `type`: `Transfer`.
- `from`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `to`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the recipient contract.
- `amount`: String representation of a 64-bit unsigned integer; amount of coins to forward.
- `color`: Hexadecimal string representation of the 256-bit (32 bytes) color of coins to forward.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Transfer",
    "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
    "amount": "10000",
    "color":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
}
```

#### TransferOut receipt

- `type`: `TransferOut`.
- `from`: Hexadecimal string representation of the 256-bit (32 bytes) contract ID of the current context if in an internal context. `null` otherwise.
- `to`: Hexadecimal string representation of the 256-bit (32 bytes) _address_ to transfer coins to.
- `amount`: String representation of a 64-bit unsigned integer; amount of coins to forward.
- `color`: Hexadecimal string representation of the 256-bit (32 bytes) color of coins to forward.
- `pc`: String representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: String representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"TransferOut",
    "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
    "amount": "10000",
    "color":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
    "pc":"18446744073709551615",
    "is":"18446744073709551614"
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

_N.B.: the default word size for the FuelVM is 8 bytes._

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
- B256: `b256`, arbitrary 256-bits value.
- Address : `address`, a 256-bit (32 bytes) address.
- Fixed size string
- Array
- Sum types
- Structs

These types are encoded in-place. Here's how to encode them. We define `enc(X)` the encoding of the type `X`.

### Unsigned Integers

`u<M>` where `M` is either 8, 16, 32, or 64 bits.

`enc(X)` is the big-endian representation of `X` left-padded with zero-bytes. Total length must be 8 bytes.

_Note: since all integer values are unsigned, there is no need to preserve the sign when extending/padding; padding with only zeroes is sufficient._

**Example:**

Encoding `42` yields: `0x000000000000002a`, which is the binary representation of the decimal number `42`, right-aligned to 8 bytes.

### Boolean

`enc(X)` is `0` if `X` is false or `1` if `X` is true, left-padded with zero-bytes. Total length must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `true` yields:

```text
0x0000000000000001
```

### Byte

`byte`, a single byte.

`enc(X)` is `X`, left-padded with zero-bytes. Total length, per argument, must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `255` yields:

```text
0x00000000000000ff
```

### B256

`b256` is a fixed size bit array of length 256. Used for 256-bit hash digests and other 256-bit types. It is encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

### Address

A 256 bits (32 bytes) address, encoded in the same way as a `B256` argument: encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

### Array

An array of a certain type `T`, `T[n]`, where `n` is the length of the array.

Arrays in Sway have fixed-length known at compile time, this means the ABI encoding for arrays also happens in-place.  

The encoding for the array contains, in order, the encoding of each element in `T[n]`, recursively following the encoding for the type `T`.

For instance, consider the function signature `my_func(bool,str[2])` with the values `(true,[1, 2])`.

The encoding will be:

1. `0x0000000000000001`, the `true` bool encoded in-place.
2. `0x0000000000000001`, First element of the parameter `str[2]`, `1`, encoded as a u8, right-aligned to 8 bytes.
3. `0x0000000000000002`, Second element of the parameter `str[2]`, `2`, encoded as a u8, right-aligned to 8 bytes.

The resulting encoded ABI will be:

```text
0x000000000000000100000000000000010000000000000002
```

### Fixed-length Strings

Strings have fixed length and are encoded in the same way as an array. `str[n]`, where `n` is the fixed-size of the string.

Note that all strings are encoded in UTF-8.

**Example:**

Encoding `"Hello, World"` as a `str[12]` **yields**:

```text
0x0000000048656c6c6f2c20576f726c64
```

Note that we're padding with zeroes in order to keep it right-aligned to 8 bytes (FuelVM's word size).

### Structs

Encoding ABIs that contain custom types, such as structs, is similar to encoding a set of primitive types. The encoding will proceed in the order that the inner types of a custom type are declared and _recursively_ just like encoding any other type. This way you can encode structs with primitive types or structs with more complex types in it, such as other structs, arrays, strings, and enums.

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
0000000000000001 // `true` encoded as a bool
0000000000000005 // `5` encoded as u8
```

More complex scenario

```rust
struct InputStruct {
    field_1: bool,
    field_2: u8[2], // An array of u8, with length 2.
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
0000000000000001 // `true` encoded as a bool
0000000000000001 // `1` encoded as u8
0000000000000002 // `2` encoded as u8
```

### Sum types (Enums)

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
0000000000000000 // The discriminant of the chosen enum, in this case `0`.
000000000000002a // `42` encoded as u64
```
