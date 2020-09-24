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
| ADD | 2 | 1 | Addition. Pop two numbers from stack and push the result onto the stack. 
| SUB | 2 | 1 | Subtraction. Pop two numbers and subtract the second popped number from the first, and push the result onto the stack.
| MUL | 2 | 1 | Multiplication. Pop two numbers and multiply them, push the result onto the stack.
| DIV | 2 | 1 | Integer division. Pop two numbers, and divide the first by the second, and push the floor of the result onto the stack.
| SDIV | 2 | 1 | Signed Division. 
| MOD | 2 | 1 | Modulo remainder. 
| SMOD | 2 | 1 | Signed modulo remainder.
| ADDMOD | 3 | 1 | Pop 2 numbers, add them, then take the modulo of that result with a 3rd popped number. Push the result onto the stack.
| MULMOD | 3 | 1 | Pop 2 numbers, multiply them, then take the modula of that result with a 3rd popped number. Push the result onto the stack.
| EXP | 2 | 1 | Pop 2 numbers, and take the power of the first number to the second number. Push the result onto the stack.
| SIGNEXTEND |  2 | 1 | Extend length of two's complement signed integer. (This will be reviewed)

#### Comparison Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| EQ | 2 | 1 | Equality comparison. Pops 2 values and pushes 1 if first value is the same as the second value, else pushes 0. Note: we do not say 'equal to' because there is no explicit/implicit type casting, e.g., as could be the case of signed integers of different 'lengths'.
| LT | 2 | 1 | Less-than comparison. Pops 2 values and pushes 1 if first value is less than second value, else pushes 0.
| GT | 2 | 1 | Greater-than comparison. Pops 2 values and pushes 1 if first value is greater than second value, else pushes 0.
| SLT | 2 | 1 | Signed less-than comparison. Pops 2 values and pushes 1 if first value is less than second value, else pushes 0. Popped values are treated as signed 256-bit integers.
| SGT | 2 | 1 | Signed greater-than comparison. Pops 2 values and pushes 1 if first value is greater than second value, else pushes 0. Popped values are treated as signed 256-bit integers.
| ISZERO | 1 | 1 | 'Simple not operator.' Pops a value from the stack and pushes 1 if the value is zero, else pushes 0. Note that this opcode is redundant with the EQ opcode with zero as the as the second stack item (parameter), but avoids pushing an extra value onto the stack at the cost of some additional implementation in the client.


#### Bitwise Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| AND | 2 | 1 | Bitwise AND. Pops 2 values and pushes the result of AND-ing their full length values.
| OR | 2 | 1 | Bitwise OR. Pops 2 values and pushes the result of OR-ing their full length values.
| XOR | 2 | 1 | Bitwise XOR. Pops 2 values and pushes the result of XOR-ing their full length values.
| NOT | 1 | 1 | Bitwise NOT. Pops a single value and pushes the result of inverting each bit.
| BYTE | 2 | 1 | Retrieve a single byte. Pops 2 values where the first value represents which byte should be returned from the second value, starting from the most significant bit, the result is pushed onto the stack. The first value must be less than 32. 
| SHL | 2 | 1 | Left shift. Pops 2 values, and pushes the result of shifting the second value left by the first value's specified number of bits, the result is pushed onto the stack.
| SHR | 2 | 1 | Right shift. Pops 2 values, and pushes the result of shifting the second value right by the first value's specified number of bits, the result is pushed onto the stack.
| SAR | 2 | 1 | Signed right shift. Pops 2 values, and pushes the result of shifting the second value right by the first value's specified number of bits, the result is pushed onto the stack. The first value is treated as an unsigned integer and the second value is treated as a signed integer.

#### Hash Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| KECCAK | 2 | 1 | Keccak-256 hash. Pops 2 values that represent the range of at least one item in memory to compute the Keccak-256 hash of. The first value is inclusive and the second value is exclusive; this means that the second value must be at least must be greater than the first value. The resulting hash is pushed onto the stack. Note: that this opcode mnemonic differs from the SHA3 opcode as specified in the yellow paper because SHA3 is not the same as Keccak. We do this to allow proper SHA3 operations in the Fuel VM. 


#### Execution Context Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| GAS | 0 | 1 | Pushes the numeric value of gas remaining onto the stack.
| BALANCE | 1 | 1 | Pops address from stack and pushes balance of given address.
| ADDRESS | 0 | 1 | Pushes address of currently executing account.
| ORIGIN | 0 | 1 | Pushes execution origin address. This is the sender of the original transaction. To review: it is never an account with non-empty associated code.
| CALLER | 0 | 1 | Pushes the address of the account that is directly responsible for this execution.
| CALLVALUE | 0 | 1 | Pushes the value deposited by the instruction/transaction responsible for this execution.
| CALLDATALOAD | 1 | 1 | Pops a value from the stack which represents the byte offset of a message call's input data, and pushes the 256-bit value at that offset of the calldata onto the stack.
| CALLDATASIZE | 0 | 1 | Pushes the size of a message call's input data.
| CALLDATACOPY | 3 | 0 | Copy calldata into memory by popping 3 values. The first value represents the offset in memory to start copying values from the call data which is offset by the second value. The number of bytes copied is equal to the 3rd popped value.
| CODESIZE | 0 | 1 | Pushes the size of the code being executed.
| CODECOPY | 3 | 1 | Copy code currently being executed into memory by popping 3 values. The first value represents the offset in memory to start copying values from the code data which is offset by the second value. The number of bytes copied is equal to the 3rd popped value.
| GASPRICE | 0 | 1 | Pushes the gas price that was set in the originating transaction.
| EXTCODESIZE | 1 | 1 | Pops an account address and pushes the size of the code at that address onto the stack.
| EXTCODECOPY | 4 | 0 | Like CODECOPY, but the first parameter for this operation is the account to copy the code of.
| RETURNDATASIZE | 0 | 1 | Push the size of the returned data from the last call in the current execution.
| RETURNDATACOPY | 3 | 0 | Copies the return data into memory. Pops 3 values where the first value specifies the address offset of the return data, the second value specifies the offset in memory to copy the data to, and the 3rd value gives the number of bytes to copy.
| EXTCODEHASH | 1 | 1 | Pushes the hash of the code at the account that is popped from the stack, else push 0.

#### Block Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| BLOCKHASH | 1 | 1 | Pops a number less than 256 and pushes hash of the block that is that many blocks behind HEAD.
| COINBASE | 0 | 1 | Pushes the block's beneficiary address.
| TIMESTAMP | 0 | 1 | Pushes the block's timestamp.
| NUMBER | 0 | 1 | Pushes the block's number.
| DIFFICULTY | 0 | 1 | Pushes the block's difficulty.
| GASLIMIT | 0 | 1 | Pushes the block's gas limit. 


#### Memory & Storage Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| MLOAD | 1 | 1 | Pops a value that represents an offset in memory whose value is pushed onto the stack.
| MSTORE | 2 | 0 | Pops two values from memory. The first value represents the offset in memory to copy the second 256-bit value into.
| MSTORE8 | 2 | 0 | Pops two values from memory. The first value represents the offset into memory to copy the second 8-bit value into.
| SLOAD | 1 | 1 | Pops a value and pushes the value in storage located at that address.
| SSTORE | 2 | 0 | Pops two values from memory. The first value represents the offset in storage to copy the second 256-bit value into.
| MSIZE | 0 | 1 | Pushes the size of active memory onto the stack.


#### Flow Operations

| Opcode | Inputs | Outputs | Semantics |
|---|---|---|---|
| JUMP | 1 | 0 | Set the program counter to the value popped from the stack.
| JUMPI | 2 | 0 | Two values are popped from the stack. If the second value is non-zero, then set the program counter to the first value popped from the stack, else, continue as before. Note: for this operand, the Yellow Paper explicitly indicates the incrementing of the program counter.
| PC | 0 | 1 | Pushes the program counter value. Note that the program counter is only incremented after successful evaluation of an operand.
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
| LOG0 | 2 | 0 | Pops two values and records a log entry with no topics based on the memory offset of the first popped value, with a length given by the second popped value.
| LOG1 | 3 | 0 | Pops two values and records a log entry with data copied from memory at an offset given by the first popped value, whose length is given by the second popped value. The topic of the log entry is given by the third popped value. 
| LOG2 | 4 | 0 | Pops two values and records a log entry with data copied from memory at an offset given by the first popped value, whose length is given by the second popped value. The two topics of the log entry are given by the third and fourth popped values.
| LOG3 | 5 | 0 | Pops two values and records a log entry with data copied from memory at an offset given by the first popped value, whose length is given by the second popped value. The three topics of the log entry are given by the next 3 popped values.
| LOG4 | 6 | 0 | Pops two values and records a log entry with data copied from memory at an offset given by the first popped value, whose length is given by the second popped value. The four topics of the log entry are given by the next 4 popped values.


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
