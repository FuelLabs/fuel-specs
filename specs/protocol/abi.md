# Contract ABI Format

This document describes and specifies the ABI (Application Binary Interface) of the FuelVM, the Sway programming language, and contracts written in Sway.

## JSON ABI Format

The JSON of an ABI is the human-readable representation of an interface call to a Sway contract. It is an array of functions containing the following properties:

- `type`: String, type of the function; right now only one is supported: `"function"`
- `name`: String, the name of the contract's function being called;
- `inputs`: An array of objects that represents the inputs to this function, each of which contains:
  - `name`: String, the name of the parameter;
  - `type`: String, the type of the parameter, can be a canonical Sway type or a custom type (struct, enum, or tuple). If it's a custom type, it must follow the format `enum | struct <custom_type_name> | tuple <types>`, e.g. `"type": "struct MyCustomStruct"`, `"type": "tuple (u64, u64)"`;
  - `components`:, Optional object, used if the `type` is a custom type, contains:
    - `name`: String, the name of the component;
    - `type`: String, the type of the component;
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

Here an example containing custom types:

```json
[
    {
        "type":"contract",
        "inputs":[
            {
                "name":"my_custom_struct",
                "type":"struct MyCustomStruct",
                "components": [
                    {
                        "name": "x",
                        "type": "u16"
                    },
                    {
                        "name": "y",
                        "type": "struct AnotherStruct",
                        "components": [
                            {
                                "name":"a",
                                "type": "bool"
                            },
                            {
                                "name":"b",
                                "type": "[u8; 2]"
                            }
                        ]
                    },
                    {
                        "name": "z",
                        "type": "tuple (u16, u8)",
                        "components": [
                            {
                                "type": "u16"
                            },
                            {
                                "type": "u8"
                            }
                        ]

                    },
                ]
            }
        ],
        "name":"takes_nested_struct",
        "outputs":[]
    }
]
```

**Important note**: The ordering of the components of a struct, enum, or tuple  _matters_ for the encoding mechanism. For instance, in the example above, we define the components `a`, then `b`, of the struct `y`. That means when encoding, a `bool` (`a`) will be encoded first, then a `u8[2]`(`b`) will be encoded. That also means that when passing values to the encoder they must also follow the specified order: `"(true,[1,2])"` should be fed into the encoder.

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
  - "ScriptResult"

Then, each receipt type will have its own properties. Some of these properties are related to the FuelVM's registers, as specified in more detail [here](../vm/instruction_set.md).

_Important note:_ For the JSON representation of receipts, we represent 64-bit unsigned integers as a JSON `String` due to limitations around the type `Number` in the JSON specification, which only supports numbers up to `2^{53-1}`, while the FuelVM's registers hold values up to `2^64`.

#### Panic receipt

- `type`: `Panic`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `reason`: Decimal string representation of an 8-bit unsigned integer; panic reason.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Panic",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "reason":"1",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### Return receipt

- `type`: `Return`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context; `null` otherwise.
- `val`: Decimal string representation of a 64-bit unsigned integer; value of register `$rA`.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Return",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val":"18446744073709551613",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### Call receipt

- `type`: `Call`.
- `from`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context; `null` otherwise.
- `to`: Hexadecimal representation of the 256-bit (32-byte) contract ID of the callee.
- `amount`: Decimal string representation of a 64-bit unsigned integer; amount of coins to forward.
- `asset_id`: Hexadecimal string representation of the 256-bit (32-byte) asset ID of coins to forward.
- `gas`: Decimal string representation of a 64-bit unsigned integer; amount gas to forward; value in register `$rD`.
- `param1`: Hexadecimal string representation of a 64-bit unsigned integer; first parameter, holds the function selector.
- `param2`: Hexadecimal string representation of a 64-bit unsigned integer; second parameter, typically used for the user-specified input to the ABI function being selected.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Call",
    "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
    "amount":"10000",
    "asset_id":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
    "gas":"500",
    "param1":"0x28f5c28f5c28f5c",
    "param2":"0x68db8bac710cb",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### Log receipt

- `type`: `Log`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `val0`: Decimal string representation of a 64-bit unsigned integer; value of register `$rA`.
- `val1`: Decimal string representation of a 64-bit unsigned integer; value of register `$rB`.
- `val2`: Decimal string representation of a 64-bit unsigned integer; value of register `$rC`.
- `val3`: Decimal string representation of a 64-bit unsigned integer; value of register `$rD`.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Log",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val0":"1844674407370",
    "val1":"1844674407371",
    "val2":"1844674407372",
    "val3":"1844674407373",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### LogData receipt

- `type`: `LogData`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `val0`: Decimal string representation of a 64-bit unsigned integer; value of register `$rA`
- `val1`: Decimal string representation of a 64-bit unsigned integer; value of register `$rB`
- `ptr`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$rC`.
- `len`: Decimal string representation of a 64-bit unsigned integer; value of register `$rD`.
- `digest`: Hexadecimal string representation of the 256-bit (32-byte) hash of `MEM[$rC, $rD]`.
- `data`: Hexadecimal string representation of the value of the memory range `MEM[$rC, $rD]`.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"LogData",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val0":"1844674407370",
    "val1":"1844674407371",
    "ptr":"0x1ad7f29abcc",
    "len":"66544",
    "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
    "pc":"0xffffffffffffffff",
    "data":"0xa7c5ac471b47",
    "is":"0xfffffffffffffffe"
}
```

#### ReturnData receipt

- `type`: `ReturnData`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `ptr`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$rA`.
- `len`: Decimal string representation of a 64-bit unsigned integer; value of register `$rB`.
- `digest`: Hexadecimal string representation of 256-bit (32-byte), hash of `MEM[$rA, $rB]`.
- `data`: Hexadecimal string representation of the value of the memory range `MEM[$rA, $rB]`.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"ReturnData",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "ptr":"0x1ad7f29abcc",
    "len":"1844",
    "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
    "pc":"0xffffffffffffffff",
    "data":"0xa7c5ac471b47",
    "is":"0xfffffffffffffffe"
}
```

#### Revert receipt

- `type`: `Revert`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `val`: Decimal string representation of a 64-bit unsigned integer; value of register `$rA`.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Revert",
    "id":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "val":"1844674407372",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### Transfer receipt

- `type`: `Transfer`.
- `from`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `to`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the recipient contract.
- `amount`: Decimal string representation of a 64-bit unsigned integer; amount of coins to forward.
- `asset_id`: Hexadecimal string representation of the 256-bit (32-byte) asset ID of coins to forward.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"Transfer",
    "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
    "amount": "10000",
    "asset_id":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### TransferOut receipt

- `type`: `TransferOut`.
- `from`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `to`: Hexadecimal string representation of the 256-bit (32-byte) _address_ to transfer coins to.
- `amount`: Decimal string representation of a 64-bit unsigned integer; amount of coins to forward.
- `asset_id`: Hexadecimal string representation of the 256-bit (32-byte) asset ID of coins to forward.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.

```json
{
    "type":"TransferOut",
    "from":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "to":"0x1c98ff5d121a6d5afc8135821acb3983e460ef0590919266d620bfc7b9b6f24d",
    "amount": "10000",
    "asset_id":"0xa5149ac6064222922eaa226526b0d853e7871e28c368f6afbcfd60a6ef8d6e61",
    "pc":"0xffffffffffffffff",
    "is":"0xfffffffffffffffe"
}
```

#### ScriptResult receipt

- `type`: `ScriptResult`.
- `result`: Hexadecimal string representation of a 64-bit unsigned integer; `0` if script exited successfully, `any` otherwise.
- `gas_used`: Decimal string representation of a 64-bit unsigned integer; amount of gas consumed by the script.

```json
{
    "type":"ScriptResult",
    "result":"0x00",
    "gas_used":"400",
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

_N.B.: the word size for the FuelVM is 8 bytes._

### Function signature

The signature is composed of the function name with the parenthesized list of comma-separated parameter types without spaces. All strings encoded with UTF-8. For custom types such as `Enum` and `Struct`, there is a prefix added to the parenthesized list (see below).

For instance, in the case of the function `entry_one` above, we would pass the string `"entry_one(u64)"` to the `sha256()` hashing algorithm. The full digest would be:

```text
0x0c36cb9cb766ff60422db243c4fff06d342949da3c64a3c6ac564941f84b6f06
```

Then we would get only the first 4 bytes of this digest and left-pad it to 8 bytes:

```text
0x000000000c36cb9c
```

#### Function with `Struct` input

- If the function has a `Struct` input type, a prefix `s` is put in front of the parenthesized list of its components

```rust
struct MyStruct {
    foo: u64,
    bar: bool,
}

fn foo(a: MyStruct);
```

has the signature

```text
foo(s(u64,bool))
```

#### Function with `Enum` input

- If the function has an `Enum` input type, a prefix `e` is put in front of the parenthesized list of its components

```rust
enum MyEnum {
    Foo: u64,
    Bar: bool,
}
fn foo(a: MyEnum);
```

has the signature

```text
foo(e(u64,bool))
```

#### Complex example for Function signature encoding

```rust
enum MyEnum {
    Foo: u64,
    Bar: bool,
}
struct MyStruct {
    bim: u8,
    bam: MyEnum,
}

fn complex_function(arg: MyStruct);
```

is encoded as:

```text
complex_function(s(u8,e(u64,bool)))
```

which is then hashed into:

```text
0x91d41b3e8c2ed1fe49678517450a32d89c9054b53f4127544e9f73ee0af704d3
```

and then the encoded function selector is:

```text
0x0000000091d41b3e
```

## Argument Encoding

When crafting transaction script data, you must encode the arguments you wish to pass to the script.

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
- Address : `address`, a 256-bit (32-byte) address.
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

A 256-bit (32-byte) address, encoded in the same way as a `b256` argument: encoded as-is.

**Example:**

Encoding `0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745` yields:

```text
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

### Array

An array of a certain type `T`, `[T; n]`, where `n` is the length of the array.

Arrays in Sway have a fixed-length which is known at compile time. This means the ABI encoding for arrays also happens in-place, with no need to account for dynamic sizing.

The encoding for the array contains, in order, the encoding of each element in `[T; n]`, recursively following the encoding for the type `T`.

For instance, consider the function signature `my_func(bool,[u64; 2])` with the values `(true,[1, 2])`.

The encoding will be:

1. `0x0000000000000001`, the `true` bool encoded in-place.
2. `0x0000000000000001`, First element of the parameter `[u64; 2]`, `1`, encoded as a `u64`.
3. `0x0000000000000002`, Second element of the parameter `[u64; 2]`, `2`, encoded as a `u64`.

The resulting encoded ABI will be:

```text
0x000000000000000100000000000000010000000000000002
```

### Fixed-length Strings

Strings have fixed length and are encoded in-place. `str[n]`, where `n` is the fixed-size of the string. Rather than padding the string, the encoding of the elements of the string is tightly packed. And unlike the other type encodings, the string encoding is left-aligned.

Note that all strings are encoded in UTF-8.

**Example:**

Encoding `"Hello, World"` as a `str[12]` **yields**:

```text
0x48656c6c6f2c20576f726c6400000000
```

Note that we're padding with zeroes in order to keep it right-aligned to 8 bytes (FuelVM's word size).

### Structs

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

Calling `bar` with `InputStruct{field_1: true, field_2: 5}` yields:

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

Calling `bar` with `InputStruct{field_1: true, field_2: [1,2]}` yields:

```plaintext
0x
0000000000000001 // `true` encoded as a bool
0000000000000001 // `1` encoded as u8
0000000000000002 // `2` encoded as u8
```

### Sum types (Enums)

ABI calls containing sum types (enums) are encoded similarly to structs: encode the discriminant first, then recursively encode the type of the enum variant being passed to the function being called.

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
