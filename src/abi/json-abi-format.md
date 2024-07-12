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

- _type declaration_: the declaration or definition of a type which can be generic. `struct Foo { .. }` and `struct Bar<T> { .. }` in the example above are both type declarations.
- _type application_: the application or use of a type. `Foo` and `Bar<u64>` in `fn baz(input1: Foo, input2: Bar<u64>);` in the example above are both applications of the type declarations `struct Foo { .. }` and `struct Bar<T> { .. }` respectively.
- _type parameter_: a generic parameter used in a type declaration. `T` in `struct Bar<T>` in the example above is a type parameter.
- _type argument_: an application of a type parameter used in a type application. `u64` in `input2: Bar<u64>` in the example above is a type argument.

## JSON ABI Spec

The ABI of a contract is represented as a JSON object containing the following properties:

- `"specVersion"`: a string representing the version pointing to this document versioning. `specVersion` enables the reader of the JSON ABI to find the correct specification for that file, this can be done by comparing it to value in [spec version](#spec-version).
- `"encodingVersion"`: a string representing the version of the `ABIEncode` and `ABIDecode` used in this program.
- `"programType"`: a string that can be `"script"`, `"contract"`, `"predicate"`, `"library"`. This is used by the SDK to generate types without having to manually specify the program type.
- `"types"`: an array describing all the _type declarations_ used (or transitively used) in the ABI. Each _type declaration_ is a JSON object that contains the following properties:
  - `"typeId"`: a unique string hash based ID. Generated as specified in [Hash Based Ids](./hash-based-ids.md).
  - `"type"`: a string representation of the _type declaration_. The section [JSON ABI Format for Each Possible Type Declaration](#json-abi-format-for-each-possible-type-declaration) specifies the format for each possible type.
  - `"components"`: an array of the components of a given type, if any, and `null` otherwise. Each component is a _type application_ represented as a JSON object that contains the following properties:
    - `"name"`: the name of the component.
    - `"type"`: the _type declaration_ hash based ID of the type of the component.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the component, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ hash based ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
  - `"typeParameters"`: an array of type hash based IDs of the _type parameters_ of the type, if the type is generic, and `null` otherwise. Each _type parameter_ is a type declaration and is represented as described in [Generic Type Parameter](#generic-type-parameter).
- `"functions`": an array describing all the functions in the ABI. Each function is a JSON object that contains the following properties:
  - `"name"`: the name of the function
  - `"inputs"`: an array of objects that represents the inputs to the function (i.e. its parameters). Each input is a _type application_ represented as a JSON object that contains the following properties:
    - `"name"`: the name of the input.
    - `"type"`: the _type declaration_ hash based ID of the type of the input.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the input, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ hash based ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
  - `"output"`: an object representing the output of the function (i.e. its return value). The output is a _type application_, which is a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ hash based ID of the type of the output.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the output, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ hash based ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
  - `"attributes"`: an optional array of _attributes_. Each _attribute_ is explained in the [dedicated section](#attributes-semantics) and is represented as a JSON object that contains the following properties:
    - `"name"`: the name of the attribute.
    - `"arguments"`: an array of attribute arguments.
- `"loggedTypes"`: an array describing all instances of [`log`](../fuel-vm/instruction-set.md#log-log-event) or [`logd`](../fuel-vm/instruction-set.md#logd-log-data-event) in the contract's bytecode. Each instance is a JSON object that contains the following properties:
  - `"logId"`: a string containing the 64bit hash based decimal ID calculated from the first 8 bytes of the `sha256` of a string that represents the type logged as defined in [Hash Based Ids](./hash-based-ids.md). The [`log`](../fuel-vm/instruction-set.md#log-log-event) and [`logd`](../fuel-vm/instruction-set.md#logd-log-data-event) instructions must set their `$rB` register to that ID.
  - `"loggedType"`: a _type application_ represented as a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ hash based ID of the type of the value being logged.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the value being logged, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ hash based ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
- `"messagesTypes"`: an array describing all instances of [`smo`](../fuel-vm/instruction-set.md#smo-send-message-to-output) in the contract's bytecode. Each instance is a JSON object that contains the following properties:
  - `"messageDataType"`: a _type application_ represented as a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ hash based ID of the type of the message data being sent.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the message data being sent, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ hash based ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
- `"configurables"`: an array describing all `configurable` variables used in the contract. Each `configurable` variable is represented as a JSON object that contains the following properties:
  - `"name"`: the name of the `configurable` variable.
  - `"configurableType"`: a _type application_ represented as a JSON object that contains the following properties:
    - `"type"`: the _type declaration_ hash based ID of the type of the `configurable` variable.
    - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the `configurable` variable, if the type is generic, and `null` otherwise. Each _type argument_ is a _type application_ represented as a JSON object that contains the following properties:
      - `"type"`: the _type declaration_ hash based ID of the type of the _type argument_.
      - `"typeArguments"`: an array of the _type arguments_ used when applying the type of the _type argument_, if the type is generic, and `null` otherwise. The format of the elements of this array recursively follows the rules described in this section.
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
  "types": [
    {
      "typeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
      "type": "b256",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
      "type": "bool",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
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
          "type": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
          "typeArguments": null
        }
      ],
      "name": "first_function",
      "output": {
        "type": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
        "typeArguments": null
      }
    },
    {
      "inputs": [
        {
          "name": "arg",
          "type": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
          "typeArguments": null
        }
      ],
      "name": "second_function",
      "output": {
        "type": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
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
  "typeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
  "type": "()",
  "components": null,
  "typeParameters": null
}
```

### `bool`

```json
{
  "typeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
  "type": "bool",
  "components": null,
  "typeParameters": null
}
```

### `u8`

```json
{
  "typeId": "c89951a24c6ca28c13fd1cfdc646b2b656d69e61a92b91023be7eb58eb914b6b",
  "type": "u8",
  "components": null,
  "typeParameters": null
}
```

### `u16`

```json
{
  "typeId": "29881aad8730c5ab11d275376323d8e4ff4179aae8ccb6c13fe4902137e162ef",
  "type": "u16",
  "components": null,
  "typeParameters": null
}
```

### `u32`

```json
{
  "typeId": "d7649d428b9ff33d188ecbf38a7e4d8fd167fa01b2e10fe9a8f9308e52f1d7cc",
  "type": "u32",
  "components": null,
  "typeParameters": null
}
```

### `u64`

```json
{
  "typeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
  "type": "u64",
  "components": null,
  "typeParameters": null
}
```

### `b256`

```json
{
  "typeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
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
  "types": [
    {
      "typeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": "625531542be70834dd127e771101ac1014111718451bfae996d97abe700c66a5",
      "type": "(_, _, _)",
      "components": [
        {
          "name": "__tuple_element",
          "type": "40c357685306e593eb4c4154377425853a7387ac5a6962d1d9198081a011d64a",
          "typeArguments": null
        },
        {
          "name": "__tuple_element",
          "type": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
          "typeArguments": null
        },
        {
          "name": "__tuple_element",
          "type": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": "40c357685306e593eb4c4154377425853a7387ac5a6962d1d9198081a011d64a",
      "type": "[_; 3]",
      "components": [
        {
          "name": "__array_element",
          "type": "84877f6e98274b9e4721db68b4c0bdb9e52b8e9572c5bd7811c07a41ced882c7",
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
      "type": "b256",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
      "type": "bool",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "83ffcfb3310e26adc9af12bd7f86d89f473ec49f37e929ef07d8b2b99cc39b30",
      "type": "enum MyEnum",
      "components": [
        {
          "name": "Foo",
          "type": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
          "typeArguments": null
        },
        {
          "name": "Bar",
          "type": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": "84877f6e98274b9e4721db68b4c0bdb9e52b8e9572c5bd7811c07a41ced882c7",
      "type": "str[5]",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
      "type": "struct MyStruct",
      "components": [
        {
          "name": "bim",
          "type": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
          "typeArguments": null
        },
        {
          "name": "bam",
          "type": "83ffcfb3310e26adc9af12bd7f86d89f473ec49f37e929ef07d8b2b99cc39b30",
          "typeArguments": null
        }
      ],
      "typeParameters": null
    },
    {
      "typeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
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
          "type": "625531542be70834dd127e771101ac1014111718451bfae996d97abe700c66a5",
          "typeArguments": null
        },
        {
          "name": "arg2",
          "type": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
          "typeArguments": null
        }
      ],
      "name": "complex_function",
      "output": {
        "type": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
        "typeArguments": null
      },
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
  "types": [
    {
      "typeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
      "type": "b256",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "83ffcfb3310e26adc9af12bd7f86d89f473ec49f37e929ef07d8b2b99cc39b30",
      "type": "enum MyEnum",
      "components": [
        {
          "name": "Foo",
          "type": "8b8c08c464656c9a4b876c13199929c5ceb37ff6c927eaeefd756c12278e98c5",
          "typeArguments": null
        },
        {
          "name": "Bar",
          "type": "037c28680d4d1fe36b9eea25fdf0b1b158fc70d022e376a17fd2cf045b416525",
          "typeArguments": null
        }
      ],
      "typeParameters": ["8b8c08c464656c9a4b876c13199929c5ceb37ff6c927eaeefd756c12278e98c5", "037c28680d4d1fe36b9eea25fdf0b1b158fc70d022e376a17fd2cf045b416525"]
    },
    {
      "typeId": "8b8c08c464656c9a4b876c13199929c5ceb37ff6c927eaeefd756c12278e98c5",
      "type": "generic T",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "037c28680d4d1fe36b9eea25fdf0b1b158fc70d022e376a17fd2cf045b416525",
      "type": "generic U",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2",
      "type": "generic W",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
      "type": "struct MyStruct",
      "components": [
        {
          "name": "bam",
          "type": "83ffcfb3310e26adc9af12bd7f86d89f473ec49f37e929ef07d8b2b99cc39b30",
          "typeArguments": [
            {
              "type": "8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2",
              "typeArguments": null
            },
            {
              "type": "8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2",
              "typeArguments": null
            }
          ]
        }
      ],
      "typeParameters": ["8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2"]
    }
  ],
  "functions": [
    {
      "inputs": [
        {
          "name": "arg1",
          "type": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
          "typeArguments": [
            {
              "type": "7c5ee1cecf5f8eacd1284feb5f0bf2bdea533a51e2f0c9aabe9236d335989f3b",
              "typeArguments": null
            }
          ]
        }
      ],
      "name": "complex_function",
      "output": {
        "type": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d ",
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
      "typeId": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
      "type": "()",
      "components": [],
      "typeParameters": null
    },
    {
      "typeId": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
      "type": "bool",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2",
      "type": "generic W",
      "components": null,
      "typeParameters": null
    },
    {
      "typeId": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
      "type": "struct MyStruct",
      "components": [
        {
          "name": "x",
          "type": "8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2",
          "typeArguments": null
        }
      ],
      "typeParameters": ["8481c239b53404729cdfbc37227da838ccb68c90cfa0412aeecd0552b73ef5d2"]
    },
    {
      "typeId": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
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
        "type": "2e38e77b22c314a449e91fafed92a43826ac6aa403ae6a8acb6cf58239fbaf5d",
        "typeArguments": null
      }
    }
  ],
  "loggedTypes": [
    {
      "logId": "12896678128313068780",
      "loggedType": {
        "type": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
        "typeArguments": [
          {
            "type": "1506e6f44c1d6291cdf46395a8e573276a4fa79e8ace3fc891e092ef32d1b0a0",
            "typeArguments": null
          }
        ]
      }
    },
    {
      "logId": "16383228984366451899",
      "loggedType": {
        "type": "eca2a040ce95fc19b7cd5f75bac530d052484d0b1a49267a2eb07a7a1b00c389",
        "typeArguments": [
          {
            "type": "b760f44fa5965c2474a3b471467a22c43185152129295af588b022ae50b50903",
            "typeArguments": null
          }
        ]
      }
    }
  ]
}
```

The `logId`s are calculated from:

- First 8 bytes of `sha256("struct MyStruct<u64>")` => `"12896678128313068780"`
- First 8 bytes of `sha256("struct MyStruct<bool>")` => `"16383228984366451899"`
