# JSON ABI Format

The JSON of an ABI is the human-readable representation of the interface of a Sway contract.

## Notation

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

## JSON ABI Spec

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
  - `"attributes"`: an optional array of attribute names.
- `"loggedTypes"`: an array describing all instances of [`log`](../../vm/instruction_set.md#log-log-event) or [`logd`](../../vm/instruction_set.md#logd-log-data-event) in the contract's bytecode. Each instance is a JSON object that contains the following properties:
  - `"logId"`: a unique integer ID. The [`log`](../../vm/instruction_set.md#log-log-event) and [`logd`](../../vm/instruction_set.md#logd-log-data-event) instructions must set their `$rB` register to that ID.
  - `"loggedType"`: a _type application_ represented as a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ ID of the type of the value being logged.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the value being logged, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
- `"configurables"`: an array describing all `configurable` variables used in the contract. Each `configurable` variable is represented as a JSON object that contains the following properties:
  - `"name"`: the name of the `configurable` variable.
  - `"configurableType"`: a _type application_ represented as a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ ID of the type of the `configurable` variable.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the `configurable` variable, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
  - `"offset"`: the specific offset within the contract's bytecode, in bytes, to the data section entry for the `configurable` variable.

> **Note**: This JSON should be both human-readable and parsable by the tooling around the FuelVM and the Sway programming language. There is a detailed specification for the binary encoding backing this readable descriptor. The [Function Selector Encoding](./fn_selector_encoding.md) section specifies the encoding for the function being selected to be executed and each of the argument types.

## A Simple Example

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

## JSON ABI Format for Each Possible Type Declaration

Below is a list of the JSON ABI formats for each possible type declaration:

### `()`

```json
{
  "typeId": <id>,
  "type": "()",
  "components": null,
  "typeParameters": null
}
```

### `bool`

```json
{
  "typeId": <id>,
  "type": "bool",
  "components": null,
  "typeParameters": null
}
```

### `u8`

```json
{
  "typeId": <id>,
  "type": "u8",
  "components": null,
  "typeParameters": null
}
```

### `u16`

```json
{
  "typeId": <id>,
  "type": "u16",
  "components": null,
  "typeParameters": null
}
```

### `u32`

```json
{
  "typeId": <id>,
  "type": "u32",
  "components": null,
  "typeParameters": null
}
```

### `u64`

```json
{
  "typeId": <id>,
  "type": "u64",
  "components": null,
  "typeParameters": null
}
```

### `b256`

```json
{
  "typeId": <id>,
  "type": "b256",
  "components": null,
  "typeParameters": null
}
```

### `struct`

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

### `enum`

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

### `str[<n>]`

```json
{
  "typeId": <id>,
  "type": "str[<n>]",
  "components": null,
  "typeParameters": null
}
```

`<n>` is the length of the string.

### `array`

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

### `tuple`

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

### Generic Type Parameter

```json
{
  "typeId": <id>,
  "type": "generic <name>",
  "components": null,
  "typeParameters": null
}
```

`<name>` is the name of the generic parameter as specified in the struct or enum declaration that uses it.

## Some Complex Examples

### An Example with Non-Generic Custom Types

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
    #[payable]
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
      },
      "attributes": ["payable"]
    }
  ],
  "loggedTypes": []
}
```

### An Example with Generic Types

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
      "typeParameters": [3, 4]
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
      "typeParameters": [5]
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

### An Example with Logs

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
      "typeParameters": [2]
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
