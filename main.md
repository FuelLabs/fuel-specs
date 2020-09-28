# Fuel VM Specification

This document gives the Fuel VM specification. 

The specification covers the types, opcodes, and execution semantics.

## Types

At this iteration, we only consider a 256-bit type, i.e., we do not distinguish between bytes and (signed/unsigned) integers. This may change as the specification evolves.

## Opcodes

This section describes the Fuel VM's opcodes. 

Many opcodes are derived from the EVM opcodes specified in the [Ethereum Yellow Paper](https://github.com/ethereum/yellowpaper).

Additional opcodes are defined for the support of optimistic rollups and performance optimizations.

### Stack-based

In this section, we describe two approaches for specifying the Fuel VM interpreter, stack-based and register-based. 

### Stop Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| STOP | 0 | 0 | Halts execution of the VM |

Note: We may add further details about cleanup of the Fuel VM, such as clearing the stack.

#### Arithmetic Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| ADD | 2 | 1 | Addition. Takes first two stack elements, adds them, and sets top stack element to the result. 
| SUB | 2 | 1 | Subtraction. Takes first two stack elements, subtracts the second number from the first, and sets top stack element to the result.
| MUL | 2 | 1 | Multiplication. Takes first two stack elements, multiplies them, and sets top stack element to the result.
| DIV | 2 | 1 | Integer division. Takes first two stack elements, divides the first by the second, and sets the top stack element to the floor of the result.
| SDIV | 2 | 1 | Signed Division. 
| MOD | 2 | 1 | Modulo remainder. 
| SMOD | 2 | 1 | Signed modulo remainder.
| ADDMOD | 3 | 1 | Takes first two stack elements, adds them, then takes the modulo of that result using the third stack element. Sets the top stack element to the result.
| MULMOD | 3 | 1 | Takes first two stack elements, multiplies them, then takes the modula of that result using the third stack element. Sets the top stack element to the result.
| EXP | 2 | 1 | Takes first two stack elements, then sets the top stack element to the result of the first number taken to the power of the second number. 
| SIGNEXTEND |  2 | 1 | Extend length of two's complement signed integer. (This will be reviewed)

#### Comparison Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| EQ | 2 | 1 | Equality comparison. Takes first two stack elements and sets top stack element to 1 if first value is the same as the second value, else sets it to 0. Note: we do not say 'equal to' because there is no explicit/implicit type casting, e.g., as could be the case of signed integers of different 'lengths'.
| LT | 2 | 1 | Less-than comparison. Takes first two stack elements values and sets top stack element to 1 if first value is less than second value, else sets it to 0.
| GT | 2 | 1 | Greater-than comparison. Takes first two stack values and sets first element to 1 if first value is greater than second value, else sets it to 0.
| SLT | 2 | 1 | Signed less-than comparison. Takes first two stack values and sets first value to 1 if first value is less than second value, else pushes 0. Values are treated as signed 256-bit integers.
| SGT | 2 | 1 | Signed greater-than comparison. Takes first two stack values and sets first value to 1 if first value is greater than second value, else sets it to 0. Values are treated as signed 256-bit integers.
| ISZERO | 1 | 1 | 'Simple not operator.' Takes first stack value and sets element to 1 if the value is zero, else sets it to 0. Note that this opcode is redundant with the EQ opcode with zero as the as the second stack item (parameter), but avoids setting an extra value on the stack at the cost of some additional implementation in the client.


#### Bitwise Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| AND | 2 | 1 | Bitwise AND. Takes top two stack elements, and sets first element to the result of AND-ing their full length values.
| OR | 2 | 1 | Bitwise OR. Takes top two stack elements, and sets first element to the result of OR-ing their full length values.
| XOR | 2 | 1 | Bitwise XOR. Takes top two stack elements, and sets first element to the result of XOR-ing their full length values.
| NOT | 1 | 1 | Bitwise NOT. Takes first stack element  and sets first element to the result of inverting each bit.
| BYTE | 2 | 1 | Retrieve a single byte. Takes top two stack elements, where the first value represents which byte should be returned from the second value, starting from the most significant bit, and sets the first stack element to the result. The first value must be less than 32. 
| SHL | 2 | 1 | Left shift. Takes top two stack elements, and sets the first stack element as the result of shifting the second value left by the first value's specified number of bits.
| SHR | 2 | 1 | Right shift. Takes top two stack elements, and sets the first stack element to the result of shifting the second value right by the first value's specified number of bits.
| SAR | 2 | 1 | Signed right shift. Takes top two stack elements, and sets the first stack element to the result of shifting the second value right by the first value's specified number of bits. The first value is treated as an unsigned integer and the second value is treated as a signed integer.

#### Hash Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| KECCAK | 2 | 1 | Keccak-256 hash. Takes top two stack elements, values which represent the range of at least one item in memory to compute the Keccak-256 hash of. The first value is inclusive and the second value is exclusive; this means that the second value must be greater than the first value. The first stack element is set to the hash value. Note: that this opcode mnemonic differs from the SHA3 opcode as specified in the yellow paper because SHA3 is not the same as Keccak. We do this to allow proper SHA3 operations in the Fuel VM. 


#### Execution Context Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| GAS | 0 | 1 | Sets the first stack element to the numeric value of gas remaining.
| BALANCE | 1 | 1 | Takes the first stack element, which is an address, and then sets the first stack element to the balance of given address.
| ADDRESS | 0 | 1 | Sets the first stack element to the address of the currently executing account.
| ORIGIN | 0 | 1 | Sets the first stack element to the execution origin address. This is the sender of the original transaction. Note: it is never an account with non-empty associated code.
| CALLER | 0 | 1 | Sets the first stack element to the address of the account that is directly responsible for this execution.
| CALLVALUE | 0 | 1 | Sets the first stack element to the value deposited by the instruction/transaction responsible for this execution.
| CALLDATALOAD | 1 | 1 | Takes the first stack element, which represents the byte offset of a message call's input data, and sets the first stack element to the 256-bit value at that offset of the calldata.
| CALLDATASIZE | 0 | 1 | Sets the first stack element to the size of a message call's input data.
| CALLDATACOPY | 3 | 0 | Copy calldata into memory by taking the first three stack values. The first value represents the offset in memory to start copying values from the call data which is offset by the second value. The number of bytes copied is equal to the third value.
| CODESIZE | 0 | 1 | Sets the first stack element to the size of the code being executed.
| CODECOPY | 3 | 1 | Copy code currently being executed into memory by taking first three stack values. The first value represents the offset in memory to start copying values from the code data which is offset by the second value. The number of bytes copied is equal to the third value.
| GASPRICE | 0 | 1 | Sets the first stack element to the gas price that was set in the originating transaction.
| EXTCODESIZE | 1 | 1 | Takes the first stack element, an account address, and sets the first stack element to the size of the code at that address.
| EXTCODECOPY | 4 | 0 | Like CODECOPY, but the first parameter for this operation is the account to copy the code of.
| RETURNDATASIZE | 0 | 1 | Sets the first stack element to the size of the returned data from the last call in the current execution.
| RETURNDATACOPY | 3 | 0 | Copies the return data into memory. Takes the first three stack values, where the first value specifies the address offset of the return data, the second value specifies the offset in memory to copy the data to, and the third value gives the number of bytes to copy.
| EXTCODEHASH | 1 | 1 | Takes the first stack element, the address, and sets the first stack element to the hash of the code at that address, else set it to 0.

#### Block Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| BLOCKHASH | 1 | 1 | Takes first stack element, a number less than 256, and sets the first stack element to the hash of the block that is that many blocks behind current HEAD.
| COINBASE | 0 | 1 | Sets the first stack element to the beneficiary address (as set by the client).
| TIMESTAMP | 0 | 1 | Sets the first stack element to the block's timestamp (as set by the client).
| NUMBER | 0 | 1 | Sets the first stack element to the block's number (as set by the client).
| DIFFICULTY | 0 | 1 | Sets the first stack element to the block's difficulty (as set by the client).
| GASLIMIT | 0 | 1 | Sets the first stack element to the block's gas limit (as set by the client). 


#### Memory & Storage Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| MLOAD | 1 | 1 | Takes first stack value that represents an offset in memory, and sets the first stack value to the value stored in memory at that offset.
| MSTORE | 2 | 0 | Takes first two stack values, where the first value represents the offset in memory to copy the second value into; the second value is a 256-bit value.
| MSTORE8 | 2 | 0 | Take first two stack values, where the first value represents the offset into memory to copy the second value into; the second value is an 8-bit value.
| SLOAD | 1 | 1 | Takes the first stack value, which is an address, and sets the first stack element to the value in storage located at that address.
| SSTORE | 2 | 0 | Takes first two stack values, where the first value represents the offset in storage to copy the second value into; the second value is a 256-bit value.
| MSIZE | 0 | 1 | Sets the first stack element to the size of active memory.


#### Flow Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| JUMP | 1 | 0 | Set the program counter to the value popped from the stack.
| JUMPI | 2 | 0 | Takes first two stack values. If the second value is non-zero, then set the program counter to the first value, else, continue as before. Note: for this operand, the Yellow Paper explicitly indicates the incrementing of the program counter; however this explicit increment is unnecessary since the operand doesn't demand special execution semantics.
| PC | 0 | 1 | Sets the first stack element to the program counter value (prior to completing this operand). Note that the program counter is only incremented after successful evaluation of an operand.
| JUMPDEST | 0 | 0 | Mark a valid destination for jumps. This operand has no effect on machine state during execution.


#### Push & Pop & Duplication & Exchange Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| POP | 0 | 1 | Pops a value from the stack.
| | | | |
| PUSHX | 0 | 1 | Pushes a X-byte value on to the stack, where X is between 1 and 32 inclusive, e.g., PUSH1 pushes a 1-byte value onto the stack. The byte values are obtained from the bytes following the current instruction in the program's code byte array.
| DUPX | 1 | 1 | Sets the X-byte value of the 1st stack item, where X is between 1 and 16 inclusive, e.g., PUSH1 pushes a 1-byte value onto the stack. Byte values are obtained from the bytes following the current instruction in the program's code byte array. Note that we opt to define the input as 1 and the output as one since the stack is not pushed. The Yellow Paper defines the number of input parameters and output parameters as X and X+1, respectively; the semantics of this interpretation is defined by the size of memory vs the number of changes to the stack.  
| SWAPX | 2 | 2 | Exchanges the 1st and Xth values on the stack, where X is between 1 and 16 inclusive, e.g., PUSH1 pushes 1 value onto the stack. The values pushes are obtained from the bytes following the current instruction in the program's code byte array. Note that we define the numbers of input and output parameters as based on the number of changes to the stack (instead of by the size of memory).


#### Logging Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| LOG0 | 2 | 0 | Takes first two stack values from stack and records a log entry with no topics based on the memory offset of the first value, with a length given by the second value.
| LOG1 | 3 | 0 | Take first three stack values and records a log entry with data copied from memory at an offset given by the first value, whose length is given by the second value. The topic of the log entry is given by the third value. 
| LOG2 | 4 | 0 | Take first four stack values and records a log entry with data copied from memory at an offset given by the first value, whose length is given by the second value. The two topics of the log entry are given by the third and fourth values.
| LOG3 | 5 | 0 | Take first five stack values and records a log entry with data copied from memory at an offset given by the first value, whose length is given by the second value. The three topics of the log entry are given by the third, fourth, and fifth values.
| LOG4 | 6 | 0 | Take first six stack values and records a log entry with data copied from memory at an offset given by the first value, whose length is given by the second value. The four topics of the log entry are given by the third, fourth, fifth, and sixth values.


#### Account Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| CREATE | 3 | 1 | Creates a new account with associated code. The value of a transfer is given as the first stack item, the input offset in memory for the associated code as the second stack item, and the input size as the third item.
| CALL | 7 | 1 | Message call into an account where the stack elements are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is pushed to the stack if the call completes successfully.
| CALLCODE | 7 | 1 | Message call into an account with the current account's code where the stack elements are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is pushed to the stack if the call completes successfully.
| RETURN | 2 | 0 | Returns values from memory using first stack value as memory offset whose size is given by the second stack value. Program execution then halts.
| DELEGATECALL | 6 | 1 | Message-call into this account with an alternative account's code, but persisting the current values for sender and value.
| CREATE2 | 4 | 1 | Create a new account, like CREATE, but use the salt of the fourth stack element.
| STATICCALL | 6 | 1 | Static message-call into an account.
| REVERT | 2 | 0 | Halt execution reverting state changes but returning data and remaining gas.
| INVALID | NA | NA | Designated invalid instruction.
| SELFDESTRUCT | 1 | 0 | Halt execution and schedule account at first stack element for deletion.


* Introspect part of the transaction - new opcodes
* A different memory model from Ethereum potentially
* Change to register machine

## Implementation

This section describes the implementation of the opcodes. 

An Ethereum 1x on-chain implementation of a Fuel VM interpreter will be available [here](https://github.com/FuelLabs/fuel-vm-evm). 

### Optimizations

This section defines optimizations that are applicable to the Fuel VM.


#### On-chain optimizations

In this section, we describe on-chain optimizations.

Potential topics:

* smart contract patterns
* compression
* passthrough
* client implementations

#### Off-chain optimizations

In this section, we describe the off-chain optimizations. 

Potential topics: 
* compression
* indexing
* parallelism


## Todo

* Add link to the yellowpaper
