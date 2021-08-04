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

This JSON should be both human-readable and parsable by the tooling around the FuelVM and the Sway programming language. However, underneath this readable format, it needs to be a well-encoded binary data. The sections below specifies the encoding for the function being selected to be executed and each of the argument types.

## Function selector 
To select which function you want to call, first, this function must be a _public_ function in a _contract_ type of a Sway program. For instance:

```rust
contract;

pub fn entry_one(arg: u64) -> u64 {
  arg
}
```

When crafting an ABI call, the first 8 bytes must be the function selector. Important to note that the default word size for the Fuel Virtual Machine is 8 bytes.

The function selector is the first 4 bytes of the Keccak-256 hash function of the signature of a public Sway contract function. FuelVM uses **Big-Endian**, so: left to right, higher order to lower. These 4 bytes are then _left-padded_ for more 4 bytes, totaling 8 bytes.

The signature is composed by only the function name with the parenthesized list of parameter types separated by a single comma without spaces. 

For instance, in the case of the function `entry_one` above, we would pass the string `"entry_one(u64)"` to the `keccak256()` hashing algorithm, the full digest would be: 
```
0x6719afacf190f86b226f603b40a0ada367cf949adc5da80e0377ee730252b2a9
```

Then we would get only the first 4 bytes of this digest and left pad it to 8 bytes:

```
0x00006719afac
```

Then, we would use `0x00006719afa` as the first part of the ABI call. 

## Argument encoding
Once you've selected the function for your ABI call and encode it (first 8 bytes), you must encode the function's arguments into it, starting from the fifth byte and onward. 

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
- Tuples (TODO)
- Sum type (TODO)
- Fixed size string (TODO)


### Static Encoding 
Static types are encoded in-place. Here's how to encode these static types. We define `enc(X)` the encoding of the type `X`. 

#### Unsigned integers (u8, u16, u32, u64)
`u<M>` where `M` is either 8, 16, 32, or 64 bits. 

`enc(X)` is the big-endian, two's complement of `X`, left-padded with zero-bytes. Total length, per argument, must be 8 bytes. 

_Note: since this is unsigned, no need to think about preserving the sign when extending/padding; padding with only zeroes is enough._

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

Encoding `entry_one(42)` yields: `0x6719afac00000002a`; `0x6719afac` being the function selector and `00000002a` being the encoded `u64` argument. 

#### Boolean
`bool`, similar to a `u8`: `enc(X)` is the big-endian, two's complement of `X`, left-padded with zero-bytes. Total length, per argument, must be 8 bytes. 

`1` equivalent to `true`, `0` equivalent to zero.

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
0x00004f0bebd300000001
```

#### Byte
`byte`, a single byte. While we could use `u8` like Rust, having a distinct type that forbids certain operations (e.g. arithmetic) without explicit casting increases type safety.

`enc(X)` is the big-endian, two's complement of `X`, padded on the **higher-order** (left) with zero-bytes. Total length, per argument, must be 8 bytes. Similar to the `bool` encoding. 

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
0x0000996725860000ffffffff
```

#### Bytes32

`bytes32` is a fixed size byte array of length 32. Used for 32-byte hash digests. 

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

Encoding `takes_bytes32("c7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745")` yields:
```
0x0000ff1e564dc7fd1d987ada439fc085cfa3c49416cf2b504ac50151e3c2335d60595cb90745
```

### Dynamic Encoding 
TODO.
