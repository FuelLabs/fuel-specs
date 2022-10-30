# Contract ABI Format

This document describes and specifies the ABI (Application Binary Interface) of the FuelVM, the Sway programming language, and contracts written in Sway.

## JSON ABI Format

The JSON of an ABI is the human-readable representation of the interface of a Sway contract.

### Notation

Before describing the format of the JSON ABI, we provide some definitions that will make the JSON ABI spec easier to read.

Given the example below:

```rust
struct Foo { x: bool }
struct Bar<T> { y: T }

fn baz(input1: Foo, input2: Bar<u64>); // an ABI function
```

we define the following expressions:

- _type declaration_: the declaration or definition of a type which can be generic. `struct Foo { .. }` and `struct Bar<T> { .. }` in the example above are both type declarations.
- _type application_: the application or use of a type. `Foo` and `Bar<u64>` in `fn baz(input1: Foo, input2: Bar<u64>);` in the example above are both applications of the type declarations `struct Foo { .. }` and `struct Bar<T> { .. }` respectively.
- _type parameter_: a generic parameter used in a type declaration. `T` in `struct Bar<T>` in the example above is a type parameter.
- _type argument_: an application of a type parameter used in a type application. `u64` in `input2: Bar<u64>` in the example above is a type argument.

### JSON ABI Spec

The ABI of a contract is represented as a JSON object containing the following properties:

- `"types`": an array describing all the _type declarations_ used (or transitively used) in the ABI. Each _type declaration_ is a JSON object that contains the following properties:
  - `"typeId"`: a unique integer ID.
  - `"type"`: a string representation of the _type declaration_. The section [JSON ABI Format for Each Possible Type Declaration](#json-abi-format-for-each-possible-type-declaration) specifies the format for each possible type.
  - `"components"`: an array of the components of a given type, if any, and `null` otherwise. Each component is a _type application_ represented as a JSON object that contains the following properties:
    - `"name"`: the name of the component.
    - `"type"`: the _type declaration_ ID of the type of the component.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the component, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
  - `"typeParameters"`: an array of type IDs of the _type parameters_ of the type, if the type is generic, and `null` otherwise. Each _type parameter_ is a type declaration and is represented as described in [Generic Type Parameter](#generic-type-parameter).
- `"functions`": an array describing all the functions in the ABI. Each function is a JSON object that contains the following properties:
  - `"name"`: the name of the function
  - `"inputs"`: an array of objects that represents the inputs to the function (i.e. its parameters). Each input is a _type application_ represented as a JSON object that contains the following properties:
    - `"name"`: the name of the input.
    - `"type"`: the _type declaration_ ID of the type of the input.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the input, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
  - `"output"`: an object representing the output of the function (i.e. its return value). The output is a _type application_, which is a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ ID of the type of the output.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the output, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
- `"loggedTypes"`: an array describing all instances of [`log`](../vm/instruction_set.md#log-log-event) or [`logd`](../vm/instruction_set.md#logd-log-data-event) in the contract's bytecode. Each instance is a JSON object that contains the following properties:
  - `"logId"`: a unique integer ID. The [`log`](../vm/instruction_set.md#log-log-event) and [`logd`](../vm/instruction_set.md#logd-log-data-event) instructions must set their `$rB` register to that ID.
  - `"loggedType"`: a _type application_ represented as a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ ID of the type of the value being logged.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the value being logged, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.

> **Note**: This JSON should be both human-readable and parsable by the tooling around the FuelVM and the Sway programming language. There is a detailed specification for the binary encoding backing this readable descriptor. The [Function Selector Encoding](#function-selector-encoding) section specifies the encoding for the function being selected to be executed and each of the argument types.

### A Simple Example

Below is a simple example showing how the JSON ABI for an example that does not use generic or complex types. We will later go over more complex examples.

```rust
abi MyContract {
    fn first_function(arg: u64) -> bool;
    fn second_function(arg: b256);
}
```

the JSON representation of this ABI looks like:

```json
{
  "types": [
    {
      "typeId": 0,
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": 1,
      "type": "b256",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 2,
      "type": "bool",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 3,
      "type": "u64",
      "components": null,
      "typeParameters": null
    }
  ],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg",
          "type": 3,
          "typeArguments": null
        }
      ],
      "name": "first_function",
      "output": {
        "type": 2,
        "typeArguments": null
      }
    },
    {
      "inputs": [
        {
          "name": "arg",
          "type": 1,
          "typeArguments": null
        }
      ],
      "name": "second_function",
      "output": {
        "type": 0,
        "typeArguments": null
      }
    }
  ],
  "loggedTypes": []
}
```

### JSON ABI Format for Each Possible Type Declaration

Below is a list of the JSON ABI formats for each possible type declaration:

#### `()`

```json
{
  "typeId": <id>,
  "type": "()",
  "components": null,
  "typeParameters": null
}
```

#### `bool`

```json
{
  "typeId": <id>,
  "type": "bool",
  "components": null,
  "typeParameters": null
}
```

#### `u8`

```json
{
  "typeId": <id>,
  "type": "u8",
  "components": null,
  "typeParameters": null
}
```

#### `u16`

```json
{
  "typeId": <id>,
  "type": "u16",
  "components": null,
  "typeParameters": null
}
```

#### `u32`

```json
{
  "typeId": <id>,
  "type": "u32",
  "components": null,
  "typeParameters": null
}
```

#### `u64`

```json
{
  "typeId": <id>,
  "type": "u64",
  "components": null,
  "typeParameters": null
}
```

#### `b256`

```json
{
  "typeId": <id>,
  "type": "b256",
  "components": null,
  "typeParameters": null
}
```

#### `struct`

```json
{
  "typeId": <id>,
  "type": "struct <struct_name>",
  "components": [
    {
      "name": "<field1_name>",
      "type": <field1_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },
    {
      "name": "<field2_name>",
      "type": <field2_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },
    ...
  ],
  "typeParameters": [
    <type_param1_type_id>,
    <type_param2_type_id>,
    ...
  ]
}
```

#### `enum`

```json
{
  "typeId": <id>,
  "type": "enum <enum_name>",
  "components": [
    {
      "name": "<variant1_name>",
      "type": <variant1_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },
    {
      "name": "<variant2_name>",
      "type": <variant2_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },
    ...
  ],
  "typeParameters": [
    <type_param1_type_id>,
    <type_param2_type_id>,
    ...
  ]
}
```

#### `str[<n>]`

```json
{
  "typeId": <id>,
  "type": "str[<n>]",
  "components": null,
  "typeParameters": null
}
```

`<n>` is the length of the string.

#### `array`

```json
{
  "typeId": <id>,
  "type": "[_; <n>]",
  "components": [
    {
      "name": "__array_element",
      "type": "<element_type>",
      "typeArguments": ...
    }
    {
      "name": "__array_element",
      "type": <element_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },

  ],
  "typeParameters": null
}
```

- `<n>` is the size of the array.

#### `tuple`

```json
{
  "typeId": <id>,
  "type": "(_, _, ...)",
  "components": [
    {
      "name": "__tuple_element",
      "type": <field1_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },
    {
      "name": "__tuple_element",
      "type": <field2_type_id>,
      "typeArguments": [
        {
          "type": <type_arg1_type_id>,
          "typeArguments": ...
        },
        {
          "type": <type_arg2_type_id>,
          "typeArguments": ...
        },
        ...
      ]
    },
    ...
  ],
  "typeParameters": null
}
```

#### Generic Type Parameter

```json
{
  "typeId": <id>,
  "type": "generic <name>",
  "components": null,
  "typeParameters": null
}
```

`<name>` is the name of the generic parameter as specified in the struct or enum declaration that uses it.

### Some Complex Examples

#### An Example with Non-Generic Custom Types

Given the following ABI declaration:

```rust
enum MyEnum {
    Foo: u64,
    Bar: bool,
}

struct MyStruct {
    bim: u64,
    bam: MyEnum,
}

abi MyContract {
    fn complex_function(
        arg1: ([str[5]; 3], bool, b256),
        arg2: MyStruct,
    );
}
```

its JSON representation would look like:

```json
{
  "types": [
    {
      "typeId": 0,
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": 1,
      "type": "(_, _, _)",
      "components": [
        {
          "name": "__tuple_element",
          "type": 2,
          "typeArguments": null
        },
        {
          "name": "__tuple_element",
          "type": 4,
          "typeArguments": null
        },
        {
          "name": "__tuple_element",
          "type": 3,
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": 2,
      "type": "[_; 3]",
      "components": [
        {
          "name": "__array_element",
          "type": 6,
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": 3,
      "type": "b256",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 4,
      "type": "bool",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 5,
      "type": "enum MyEnum",
      "components": [
        {
          "name": "Foo",
          "type": 8,
          "typeArguments": null
        },
        {
          "name": "Bar",
          "type": 4,
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": 6,
      "type": "str[5]",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 7,
      "type": "struct MyStruct",
      "components": [
        {
          "name": "bim",
          "type": 8,
          "typeArguments": null
        },
        {
          "name": "bam",
          "type": 5,
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": 8,
      "type": "u64",
      "components": null,
      "typeParameters": null
    }
  ],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg1",
          "type": 1,
          "typeArguments": null
        },
        {
          "name": "arg2",
          "type": 7,
          "typeArguments": null
        }
      ],
      "name": "complex_function",
      "output": {
        "type": 0,
        "typeArguments": null
      }
    }
  ],
  "loggedTypes": []
}
```

#### An Example with Generic Types

Given the following ABI declaration:

```rust
enum MyEnum<T, U> {
    Foo: T,
    Bar: U,
}
struct MyStruct<W> {
    bam: MyEnum<W, W>,
}

abi MyContract {
    fn complex_function(
        arg1: MyStruct<b256>,
    );
}
```

its JSON representation would look like:

```json
{
  "types": [
    {
      "typeId": 0,
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": 1,
      "type": "b256",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 2,
      "type": "enum MyEnum",
      "components": [
        {
          "name": "Foo",
          "type": 3,
          "typeArguments": null
        },
        {
          "name": "Bar",
          "type": 4,
          "typeArguments": null
        }
      ],
      "typeParameters": [
        3,
        4
      ]
    },
    {
      "typeId": 3,
      "type": "generic T",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 4,
      "type": "generic U",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 5,
      "type": "generic W",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 6,
      "type": "struct MyStruct",
      "components": [
        {
          "name": "bam",
          "type": 2,
          "typeArguments": [
            {
              "type": 5,
              "typeArguments": null
            },
            {
              "type": 5,
              "typeArguments": null
            }
          ]
        }
      ],
      "typeParameters": [
        5
      ]
    }
  ],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg1",
          "type": 6,
          "typeArguments": [
            {
              "type": 1,
              "typeArguments": null
            }
          ]
        }
      ],
      "name": "complex_function",
      "output": {
        "type": 0,
        "typeArguments": null
      }
    }
  ],
  "loggedTypes": []
}
```

#### An Example with Logs

Given the following contract:

```rust
struct MyStruct<W> {
    x: W,
}

abi MyContract {
    fn logging();
}

...

fn logging() {
    log(MyStruct { x: 42 });
    log(MyStruct { x: true });
}
```

its JSON representation would look like:

```json
{
  "types": [
    {
      "typeId": 0,
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": 1,
      "type": "bool",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 2,
      "type": "generic W",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": 3,
      "type": "struct MyStruct",
      "components": [
        {
          "name": "x",
          "type": 2,
          "typeArguments": null
        }
      ],
      "typeParameters": [
        2
      ]
    },
    {
      "typeId": 4,
      "type": "u64",
      "components": null,
      "typeParameters": null
    }
  ],
  "functions": [
    {
      "inputs": [],
      "name": "logging",
      "output": {
        "type": 0,
        "typeArguments": null
      }
    }
  ],
  "loggedTypes": [
    {
      "logId": 0,
      "loggedType": {
        "type": 3,
        "typeArguments": [
          {
            "type": 4,
            "typeArguments": null
          }
        ]
      }
    },
    {
      "logId": 1,
      "loggedType": {
        "type": 3,
        "typeArguments": [
          {
            "type": 1,
            "typeArguments": null
          }
        ]
      }
    }
  ]
}
```

## Receipt

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
  - [Call](#call-receipt)
  - [Return](#return-receipt)
  - [ReturnData](#returndata-receipt)
  - [Panic](#panic-receipt)
  - [Revert](#revert-receipt)
  - [Log](#log-receipt)
  - [LogData](#logdata-receipt)
  - [Transfer](#transfer-receipt)
  - [TransferOut](#transferout-receipt)
  - [ScriptResult](#scriptresult-receipt)
  - [MessageOut](#messageout-receipt)

Then, each receipt type will have its own properties. Some of these properties are related to the FuelVM's registers, as specified in more detail [here](../vm/instruction_set.md).

_Important note:_ For the JSON representation of receipts, we represent 64-bit unsigned integers as a JSON `String` due to limitations around the type `Number` in the JSON specification, which only supports numbers up to `2^{53-1}`, while the FuelVM's registers hold values up to `2^64`.

### Panic receipt

- `type`: `Panic`.
- `id`: Hexadecimal string representation of the 256-bit (32-byte) contract ID of the current context if in an internal context. `null` otherwise.
- `reason`: Decimal string representation of an 8-bit unsigned integer; panic reason.
- `pc`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$pc`.
- `is`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$is`.
- `contractId`: Optional hexadecimal string representation of the 256-bit (32-byte) contract ID if applicable. `null` otherwise.
Not included in canonical receipt form. Primary use is for access-list estimation by SDK.

```json
{
  "type": "Panic",
  "id": "0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
  "reason": "1",
  "pc": "0xffffffffffffffff",
  "is": "0xfffffffffffffffe",
  "contractId": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

### Return receipt

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

### Call receipt

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

### Log receipt

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

### LogData receipt

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

### ReturnData receipt

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

### Revert receipt

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

### Transfer receipt

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

### TransferOut receipt

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

### ScriptResult receipt

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

### MessageOut receipt

- `type`: `MessageOut`.
- `messageID`: Hexadecimal string representation of the 256-bit (32-byte) message ID as described [here](../protocol/identifiers.md#message-id).
- `sender`: Hexadecimal string representation of the 256-bit (32-byte) address of the message sender: `MEM[$fp, 32]`.
- `recipient`: Hexadecimal string representation of the 256-bit (32-byte) address of the message recipient: `MEM[$rA, 32]`.
- `amount`: Hexadecimal string representation of a 64-bit unsigned integer; value of register `$rD`.
- `nonce`: Hexadecimal string representation of the 256-bit (32-byte) message nonce as described [here](../protocol/identifiers.md#message-nonce).
- `len`: Decimal string representation of a 16-bit unsigned integer; value of register `$rB`.
- `digest`: Hexadecimal string representation of 256-bit (32-byte), hash of `MEM[$rA + 32, $rB]`.
- `data`: Hexadecimal string representation of the value of the memory range `MEM[$rA + 32, $rB]`.

```json
{
    "type":"MessageOut",
    "messageID":"0x39150017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "sender":"0x38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff05139150017c9e",
    "recipient":"0x4710162c2e3a95a6faff05139150017c9e38e5e280432d546fae345d6ce6d8fe",
    "amount":"0xe6d8fe4710162c2e",
    "nonce":"0x47101017c9e38e5e280432d546fae345d6ce6d8fe4710162c2e3a95a6faff051",
    "len":"65535",
    "digest":"0xd28b78894e493c98a196aa51b432b674e4813253257ed9331054ee8d6813b3aa",
    "data":"0xa7c5ac471b47"
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

> **Note**: The word size for the FuelVM is 8 bytes.

### Function signature

The signature is composed of the function name with the parenthesized list of comma-separated parameter types without spaces. All strings encoded with UTF-8. For custom types such as `enum` and `struct`, there is a prefix added to the parenthesized list (see below). Generic `struct` and `enum` types also accept a list of comma-separated type arguments in between angle brackets right after the prefix.

For instance, to compute the selector for the following function:

```rust
    fn entry_one(arg: u64);
```

we should pass `"entry_one(u64)"` to the `sha256()` hashing algorithm. The full digest would be:

```text
0x0c36cb9cb766ff60422db243c4fff06d342949da3c64a3c6ac564941f84b6f06
```

Then we would get only the first 4 bytes of this digest and left-pad it to 8 bytes:

```text
0x000000000c36cb9c
```

The table below summarizes how each function argument type is encoded

| Type       | Encoding                                                                                                                                                                    |
|------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `bool`     | `bool`                                                                                                                                                                      |
| `u8`       | `u8`                                                                                                                                                                        |
| `u16`      | `u16`                                                                                                                                                                       |
| `u32`      | `u32`                                                                                                                                                                       |
| `u64`      | `u64`                                                                                                                                                                       |
| `b256`     | `b256`                                                                                                                                                                      |
| `struct`   | `s<<arg1>,<arg2>,...>(<ty1>,<ty2>,...)` where `<ty1>`, `<ty2>`, ... are the encoded types of the struct fields and `<arg1>`, `<arg2>`, ... are the encoded type arguments   |
| `enum`     | `e<<arg1>>,<arg_2>,...>(<ty1>,<ty2>,...)` where `<ty1>`, `<ty2>`, ... are the encoded types of the enum variants and `<arg1>`, `<arg2>`, ... are the encoded type arguments |
| `str[<n>]` | `str[<n>]`                                                                                                                                                                  |
| `array`    | `a[<ty>;<n>]` where `<ty>` is the encoded element type of the array and `<n>` is its length                                                                                 |
| `tuple`    | `(<ty1>,<ty2>,...)` where `<ty1>`, `<ty2>`, ... are the encoded types of the tuple fields                                                                                   |

> **Note:** Non-generic structs and enums do not require angle brackets.

### A Complex Example

```rust
enum MyEnum<V> {
    Foo: u64,
    Bar: bool,
}
struct MyStruct<T, U> {
    bim: T,
    bam: MyEnum<u64>,
}

struct MyOtherStruct {
    bom: u64,
}

fn complex_function(
    arg1: MyStruct<[b256; 3], u8>,
    arg2: [MyStruct<u64, bool>; 4],
    arg3: (str[5], bool),
    arg4: MyOtherStruct,
);
```

is encoded as:

```text
abi MyContract {
    complex_function(s<a[b256;3],u8>(a[b256;3],e<u64>(u64,bool)),a[s<u64,bool>(u64,e<u64>(u64,bool));4],(str[5],bool),s(u64))
}
```

which is then hashed into:

```text
51fdfdadc37ff569e281a622281af7ec055f8098c40bc566118cbb48ca5fd28b
```

and then the encoded function selector is:

```text
0x0000000051fdfdad
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

> **Note:** since all integer values are unsigned, there is no need to preserve the sign when extending/padding; padding with only zeroes is sufficient._

**Example:**

Encoding `42` yields: `0x000000000000002a`, which is the binary representation of the decimal number `42`, right-aligned to 8 bytes.

### Boolean

`enc(X)` is `0` if `X` is false or `1` if `X` is true, left-padded with zero-bytes. Total length must be 8 bytes. Similar to the `u8` encoding.

**Example:**

Encoding `true` yields:

```text
0x0000000000000001
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
