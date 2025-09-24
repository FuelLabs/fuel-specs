# JSON ABI Format

The JSON of an ABI is the human-readable representation of the interface of a Sway contract.

## Spec Version

Current `specVersion` is `1.0`

The version above should be updated each time this spec changes and the JSON ABI generator should be updated with the new changes along with an increment in the spec version.

## Notation

Before describing the format of the JSON ABI, we provide some definitions that will make the JSON ABI spec easier to read.

Given the example below:

```rust
struct Foo { x: bool }
struct Bar<T> { y: T }

fn baz(input1: Foo, input2: Bar<u64>); // an ABI function
```

we define the following expressions:

- _type concrete declaration_: the declaration or definition of a type which can be generic. `struct Foo { .. }` and `struct Bar<T> { .. }` in the example above are both type declarations. Note that generic types may have multiple _type concrete declaration_.
- _type metadata declaration_: the declaration or definition of a type which can be generic. `struct Foo { .. }` and `struct Bar<T> { .. }` in the example above are both type declarations. The metadata declaration contains component details about the type. And a single _type metadata declaration_ is generated per type, even for generic types.
- _type application_: the application or use of a type. `Foo` and `Bar<u64>` in `fn baz(input1: Foo, input2: Bar<u64>);` in the example above are both applications of the type declarations `struct Foo { .. }` and `struct Bar<T> { .. }` respectively.
- _type parameter_: a generic parameter used in a type declaration. `T` in `struct Bar<T>` in the example above is a type parameter.
- _type argument_: an application of a type parameter used in a type application. `u64` in `input2: Bar<u64>` in the example above is a type argument.

## JSON ABI Spec

The ABI of a contract is represented as a JSON object containing the following properties:

- `"specVersion"`: a string representing the version pointing to this document versioning. `specVersion` enables the reader of the JSON ABI to find the correct specification for that file, this can be done by comparing it to value in [spec version](#spec-version).
- `"encodingVersion"`: a string representing the version of the `ABIEncode` and `ABIDecode` used in this program.
- `"programType"`: a string that can be `"script"`, `"contract"`, `"predicate"`, `"library"`. This is used by the SDK to generate types without having to manually specify the program type.
- `"concreteTypes"`: an array describing all the _type concrete declarations_ used (or transitively used) in the ABI. Each _type concrete declaration_ is a JSON object that contains the following properties:
  - `"type"`: a string representing the type, the `sha256` of this string generates the `concreteTypeId`.
  - `"concreteTypeId"`: a unique string hash based ID. Generated as specified in [Hash Based Ids](./hash-based-ids.md).
  - `"metadataTypeId"`: the _type metadata declaration_ ID of this type, if the type metadata has components or is generic, otherwise nonexistent.
  - `"typeArguments"`: an array of _type concrete declarations_ hash based IDs of the type parameters of the type, if the type is generic, otherwise nonexistent.
- `"metadataTypes"`: an array describing all the _type metadata declarations_ used (or transitively used) in the ABI. Each _type metadata declaration_ is a JSON object that contains the following properties:
  - `"type"`: a string representation of the _type metadata declaration_. The section [JSON ABI Format for Each Possible Metadata Type Declaration](#json-abi-format-for-each-possible-metadata-type-declaration) specifies the format for each possible type.
  - `"metadataTypeId"`: a unique integer ID.
  - `"components"`: an array of the components of a given type, if any, otherwise nonexistent. Each component is a _type application_ represented as a JSON object that contains the following properties:
    - `"name"`: the name of the component.
    - `"typeId"`: the _type metadata declaration_ ID (number) or _type concrete declaration_ hash based ID (string) of the type of the component.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the component, if the type is generic, otherwise nonexistent. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"name"`: the name of the _type argument_.
      - `"typeId"`: the _type metadata declaration_ ID (number) or _type concrete declaration_ hash based ID (string) of the type of the component.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, otherwise nonexistent. The format of the elements of this array recursively follows the rules described in this section.
  - `"typeParameters"`: an array of _type metadata declaration_ ID of the _type parameters_ of the type, if the type is generic, otherwise nonexistent. Each _type parameter_ is a type declaration and is represented as described in [Generic Type Parameter](#generic-type-parameter).
- `"functions`": an array describing all the functions in the ABI. Each function is a JSON object that contains the following properties:
  - `"name"`: the name of the function
  - `"inputs"`: an array of objects that represents the inputs to the function (i.e. its parameters). Each input is a _type application_ represented as a JSON object that contains the following properties:
    - `"name"`: the name of the input.
    - `"concreteTypeId"`: the _type concrete declaration_ hash based ID of the type of the input.
  - `"output"`: the _type concrete declaration_ hash based ID of the type being returned by the function.
  - `"attributes"`: an optional array of _attributes_. Each _attribute_ is explained in the [dedicated section](#attributes-semantics) and is represented as a JSON object that contains the following properties:
    - `"name"`: the name of the attribute.
    - `"arguments"`: an array of attribute arguments.
- `"loggedTypes"`: an array describing all instances of [`log`](../fuel-vm/instruction-set.md#log-log-event) or [`logd`](../fuel-vm/instruction-set.md#logd-log-data-event) in the contract's bytecode. Each instance is a JSON object that contains the following properties:
  - `"logId"`: a string containing the 64bit hash based decimal ID calculated from the first 8 bytes of the `sha256` of a string that represents the type logged as defined in [Hash Based Ids](./hash-based-ids.md). The [`log`](../fuel-vm/instruction-set.md#log-log-event) and [`logd`](../fuel-vm/instruction-set.md#logd-log-data-event) instructions must set their `$rB` register to that ID.
  - `"concreteTypeId"`: the _type concrete declaration_ hash based ID of the value being logged.
- `"messagesTypes"`: an array describing all instances of [`smo`](../fuel-vm/instruction-set.md#smo-send-message-to-output) in the contract's bytecode. Each instance is a JSON object that contains the following properties:
  - `"messageId"`: a unique string ID.
  - `"concreteTypeId"`: the _type concrete declaration_ hash based ID of the message data being sent.
- `"configurables"`: an array describing all `configurable` variables used in the contract. Each `configurable` variable is represented as a JSON object that contains the following properties:
  - `"name"`: the name of the `configurable` variable.
  - `"concreteTypeId"`: the _type concrete declaration_ hash based ID of the type of the `configurable` variable.
  - `"offset"`: the specific offset within the contract's bytecode, in bytes, to the data section entry for the `configurable` variable.

> **Note**: This JSON should be both human-readable and parsable by the tooling around the FuelVM and the Sway programming language. There is a detailed specification for the binary encoding backing this readable descriptor. The [Function Selector Encoding](./fn-selector-encoding.md) section specifies the encoding for the function being selected to be executed and each of the argument types.

### Attributes Semantics

| Attribute name | Attribute arguments               | Semantics                                                                                              |
|----------------|-----------------------------------|--------------------------------------------------------------------------------------------------------|
| `storage`      | `read` and/or `write`             | Specifies if a function reads or writes to/from storage                                                |
| `payable`      | None                              | Specifies if a function can accept coins: a function without `payable` attribute must not accept coins |
| `test`         | None                              | Specifies if a function is a unit test                                                                 |
| `inline`       | `never` or `always`, but not both | Specifies if a function should be inlined during code generation                                       |
| `doc-comment`  | String                            | Documentation comment                                                                                  |
| `doc`          | Not defined yet                   | Not defined yet                                                                                        |

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
  "concreteTypes": [
    {
      "type": "u64",
      "concreteTypeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0"
    },
    {
      "type": "b256",
      "concreteTypeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b"
    },
    {
      "type": "bool",
      "concreteTypeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903"
    },
    {
      "type": "()",
      "concreteTypeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d"
    }
  ],
  "metadataTypes": [],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg",
          "concreteTypeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0"
        }
      ],
      "name": "first_function",
      "output": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903"
    },
    {
      "inputs": [
        {
          "name": "arg",
          "concreteTypeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b"
        }
      ],
      "name": "second_function",
      "output": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d"
    }
  ],
  "loggedTypes": []
}
```

## JSON ABI Format for Each Possible Metadata Type Declaration

Below is a list of the JSON ABI formats for each possible metadata type declaration:

### `struct`

```json
{
  "metadataTypeId": <id>,
  "type": "struct <struct_name>",
  "components": [
    {
      "name": "<field1_name>",
      "typeId": "<field1_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
          "typeArguments": ...
        },
        ...
      ]
    },
    {
      "name": "<field2_name>",
      "typeId": "<field2_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
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
  "metadataTypeId": <id>,
  "type": "enum <enum_name>",
  "components": [
    {
      "name": "<variant1_name>",
      "typeId": "<variant1_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
          "typeArguments": ...
        },
        ...
      ]
    },
    {
      "name": "<variant2_name>",
      "typeId": "<variant2_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
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

### `array`

```json
{
  "metadataTypeId": <id>,
  "type": "[_; <n>]",
  "components": [
    {
      "name": "__array_element",
      "typeId": "<element_type>",
      "typeArguments": ...
    }
    {
      "name": "__array_element",
      "typeId": "<element_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
          "typeArguments": ...
        },
        ...
      ]
    },
  ]
}
```

- `<n>` is the size of the array.

### `tuple`

```json
{
  "metadataTypeId": <id>,
  "type": "(_, _, ...)",
  "components": [
    {
      "name": "__tuple_element",
      "typeId": "<field1_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
          "typeArguments": ...
        },
        ...
      ]
    },
    {
      "name": "__tuple_element",
      "typeId": "<field2_type_id>",
      "typeArguments": [
        {
          "typeId": "<type_arg1_type_id>",
          "typeArguments": ...
        },
        {
          "typeId": "<type_arg2_type_id>",
          "typeArguments": ...
        },
        ...
      ]
    },
    ...
  ]
}
```

### Generic Type Parameter

```json
{
  "metadataTypeId": <id>,
  "type": "generic <name>"
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
    /// this is a doc comment
    #[payable, storage(read, write)]
    fn complex_function(
        arg1: ([str[5]; 3], bool, b256),
        arg2: MyStruct,
    );
}
```

its JSON representation would look like:

```json
{
  "concreteTypes": [
    {
      "type": "([str[5]; 3], bool, b256)",
      "concreteTypeId": "625531542be70834dd127e771101ac1014111718451bfae996d97abe700c66a5",
      "metadataTypeId": 1,
    },
    {
      "type": "str[5]",
      "concreteTypeId": "84877f6e98274b9e4721db68b4c0bdb9e52b8e9572c5bd7811c07a41ced882c7",
    },
    {
      "type": "struct MyStruct",
      "concreteTypeId": "392d58c694d2d91f3025f2bccfadacf2a105936f5da881b0899185d49f264522",
      "metadataTypeId": 4,
    },
    {
      "type": "u64",
      "concreteTypeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0"
    },
    {
      "type": "b256",
      "concreteTypeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b"
    },
    {
      "type": "bool",
      "concreteTypeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903"
    },
    {
      "type": "()",
      "concreteTypeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d"
    }
  ],
  "metadataTypes": [
    {
      "metadataTypeId": 1,
      "type": "(_, _, _)",
      "components": [
        {
          "name": "__tuple_element",
          "typeId": 2,
        },
        {
          "name": "__tuple_element",
          "typeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
        },
        {
          "name": "__tuple_element",
          "typeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
        }
      ]
    },
    {
      "metadataTypeId": 2,
      "type": "[_; 3]",
      "components": [
        {
          "name": "__array_element",
          "typeId": "84877f6e98274b9e4721db68b4c0bdb9e52b8e9572c5bd7811c07a41ced882c7",
        }
      ]
    },
    {
      "metadataTypeId": 3,
      "type": "enum MyEnum",
      "components": [
        {
          "name": "Foo",
          "typeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
        },
        {
          "name": "Bar",
          "typeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
        }
      ]
    },
    {
      "metadataTypeId": 4,
      "type": "struct MyStruct",
      "components": [
        {
          "name": "bim",
          "typeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
        },
        {
          "name": "bam",
          "typeId": 3,
        }
      ]
    },
  ],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg1",
          "concreteTypeId": "625531542be70834dd127e771101ac1014111718451bfae996d97abe700c66a5",
        },
        {
          "name": "arg2",
          "concreteTypeId": "392d58c694d2d91f3025f2bccfadacf2a105936f5da881b0899185d49f264522"
        }
      ],
      "name": "complex_function",
      "output": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
      "attributes": [
        {
          "name": "doc-comment",
          "arguments": [" this is a doc comment"]
        },
        {
          "name": "payable",
        },
        {
          "name": "storage",
          "arguments": ["read", "write"]
        }
      ]
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
  "concreteTypes": [
    {
      "type": "struct MyStruct<b256>",
      "concreteTypeId": "3ddd5c1768dd7869663dc2f868ea8a8ce68bd6064244dbc4286e2c921c8ce962",
      "metadataTypeId": 5,
      "typeArguments": [
        "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b"
      ]
    },
    {
      "type": "b256",
      "concreteTypeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
    },
    {
      "type": "()",
      "concreteTypeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
    }
  ],
  "metadataTypes": [
    {
      "metadataTypeId": 1,
      "type": "enum MyEnum",
      "components": [
        {
          "name": "Foo",
          "typeId": 2,
        },
        {
          "name": "Bar",
          "typeId": 3,
        }
      ],
      "typeParameters": [2, 3]
    },
    {
      "metadataTypeId": 2,
      "type": "generic T",
    },
    {
      "metadataTypeId": 3,
      "type": "generic U",
    },
    {
      "metadataTypeId": 4,
      "type": "generic W",
    },
    {
      "metadataTypeId": 5,
      "type": "struct MyStruct",
      "components": [
        {
          "name": "bam",
          "typeId": 1,
          "typeArguments": [
            {
              "typeId": 4,
            },
            {
              "typeId": 4,
            }
          ]
        }
      ],
      "typeParameters": [4]
    }
  ],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg1",
          "concreteTypeId": "3ddd5c1768dd7869663dc2f868ea8a8ce68bd6064244dbc4286e2c921c8ce962"
        }
      ],
      "name": "complex_function",
      "output": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d"
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
  "concreteTypes": [
    {      
      "type": "struct MyStruct<bool>",
      "concreteTypeId": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
      "metadataTypeId": 1,
      "typeArguments": [
        "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903"
      ]
    },
    {      
      "type": "struct MyStruct<u64>",
      "concreteTypeId": "b2fa346d9ca66ceca61951a27dba2977b2a82b8aa8600670604f286a1393dffe",
      "metadataTypeId": 1,
      "typeArguments": [
        "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0"
      ]
    },
    {      
      "type": "bool",
      "concreteTypeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
    },
    {      
      "type": "u64",
      "concreteTypeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
    },
    {
      "type": "()",
      "concreteTypeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
    }
  ],
  "metadataTypes": [
    {
      "metadataTypeId": 1,
      "type": "struct MyStruct",
      "components": [
        {
          "name": "x",
          "typeId": 2,
          "typeArguments": null
        }
      ],
      "typeParameters": [2]
    },
    {
      "metadataTypeId": 2,
      "type": "generic W",
    },
  ],
  "functions": [
    {
      "inputs": [],
      "name": "logging",
      "output": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d"
    }
  ],
  "loggedTypes": [
    {
      "logId": "12896678128313068780",
      "concreteTypeId": "b2fa346d9ca66ceca61951a27dba2977b2a82b8aa8600670604f286a1393dffe"
    },
    {
      "logId": "16383228984366451899",
      "concreteTypeId": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389"
    }
  ]
}
```

The `logIds` are calculated from:

- First 8 bytes of `sha256("struct MyStruct<u64>")` => "12896678128313068780"
- First 8 bytes of `sha256("struct MyStruct<bool>")` => "16383228984366451899"
