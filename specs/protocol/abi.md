# FuelVM ABI Format

This document describes and specifies the ABI (Application Binary Interface) of the Fuel Virtual Machine, the Sway programming language, and Contracts written in Sway.  

## JSON ABI Format
The JSON of an ABI is the human-readable representation of an interface call to a Sway contract. It is an array of functions containing the following properties:
-   `type`: String, type of the function; right now only one is supported: `"contract"`
-   `name`: String, the name of the contract's function being called;
-   `inputs`: An array of objects that represents the inputs to this function, each of which contains:
    -   `name`: String, the name of the parameter;
    -   `type`: String, the canonical Sway type of the parameter;
-   `outputs`: An array of objects similar to `inputs`;

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

The signature is composed of the function name with the parenthesized list of comma-separated parameter types without spaces. 

For instance, in the case of the function `entry_one` above, we would pass the string `"entry_one(u64)"` to the `sha256()` hashing algorithm, the full digest would be: 
```
0x0c36cb9cb766ff60422db243c4fff06d342949da3c64a3c6ac564941f84b6f06
```

Then we would get only the first 4 bytes of this digest and left pad it to 8 bytes:

```
0x00000c36cb9c
```

## Argument encoding
When crafting a Sway script data, you must encode the arguments you wish to pass.  

**The encoding for the argument will depend on the type of the argument being encoded.**

###  Types
These are the available types that can be encoded in the ABI:
- Unsigned integers: 
	- `u8`, 8 bits.
	- `u16`, 16 bits.
	- `u32`, 32 bits.
	- `u64`, 64 bits.
- Boolean: `bool`, either `0` or `1` encoded similarly to `u8`.
- Byte: `byte`, single byte.
- Bytes32: `bytes32`, 32-byte hash digest.
- Address : `address`, a 32-byte address. (TODO)
- Array (TODO)
- Sum type (TODO)
- Fixed size string (TODO)
- Structs (TODO)


### Static Encoding 
Static types are encoded in-place. Here's how to encode these static types. We define `enc(X)` the encoding of the type `X`. 

#### Unsigned integers (u8, u16, u32, u64)
`u<M>` where `M` is either 8, 16, 32, or 64 bits. 

`enc(X)` is the big-endian representation of `X` left-padded with zero-bytes. Total length, per argument, must be 8 bytes. 

_Note: since all integer values are unsigned, there is no need to preserve the sign when extending/padding; padding with only zeroes is sufficient._

Example:

```json
[
   {
      "type":"contract",
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

Encoding `entry_one(42)` yields: `0x00000002a`, which is the binary representation of the decimal number `42`, right-aligned to 8 bytes.

#### Boolean
`bool`, similar to a `u8`: `enc(X)` is the big-endian, right-aligned to 8 bytes, left-padded with zeroes. Total length, per argument, must be 8 bytes. 

`1` equivalent to `true`, `0` equivalent to `false`.

Example:
```json
[
   {
      "type":"function",
      "inputs":[
         {
            "name":"arg",
            "type":"bool"
         }
      ],
      "name":"bool_check",
      "outputs":[
         
      ]
   }
]
```
Encoding `bool_check(true)` yields:
```
0x00000001
```

#### Byte
`byte`, a single byte.

`enc(X)` is the big-endian, two's complement of `X`, left-padded with zero-bytes. Total length, per argument, must be 8 bytes. Similar to the `bool` encoding. 

Example: 

```json
[
   {
      "type":"function",
      "inputs":[
         {
            "name":"arg",
            "type":"byte"
         }
      ],
      "name":"takes_one_byte",
      "outputs":[
         
      ]
   }
]
```

Encoding `takes_one_byte(255)` yields:
```
0x0000ffffffff
```

#### Bytes32

`bytes32` is a fixed size byte array of length 32. Used for 32-byte hash digests. It's encoded as-is.

Example:
```json
[
   {
      "type":"function",
      "inputs":[
         {
            "name":"arg",
            "type":"bytes32"
         }
      ],
      "name":"takes_bytes32",
      "outputs":[
         
      ]
   }
]
```

Encoding `takes_bytes32(0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745)` yields:
```
0xc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

### Dynamic Encoding 
TODO.
