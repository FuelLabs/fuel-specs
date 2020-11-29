# FuelVM Opcodes

- [Arithmetic/Logic (ALU) Operands](#arithmeticlogic-alu-operands)
  - [ADD: Add](#add-add)
  - [ADDI: Add immediate](#addi-add-immediate)
  - [AND: AND](#and-and)
  - [ANDI: AND immediate](#andi-and-immediate)
  - [CPSR: Copy from special register](#cpsr-copy-from-special-register)
  - [DIV: Divide](#div-divide)
  - [DIVI: Divide immediate](#divi-divide-immediate)
  - [EQ: Equals](#eq-equals)
  - [EXP: Exponentiate](#exp-exponentiate)
  - [EXPI: Exponentiate immediate](#expi-exponentiate-immediate)
  - [GT: Greater than](#gt-greater-than)
  - [J: Jump](#j-jump)
  - [JI: Jump immediate](#ji-jump-immediate)
  - [LB: Load byte](#lb-load-byte)
  - [LW: Load word](#lw-load-word)
  - [MOD: Modulus](#mod-modulus)
  - [MODI: Modulus immediate](#modi-modulus-immediate)
  - [MUL: Multiply](#mul-multiply)
  - [NOOP: No operation](#noop-no-operation)
  - [OR: OR](#or-or)
  - [ORI: OR immediate](#ori-or-immediate)
  - [SB: Store byte](#sb-store-byte)
  - [SLL: Shift left logical](#sll-shift-left-logical)
  - [SLLI: Shift left logical immediate](#slli-shift-left-logical-immediate)
  - [SRA: Shift right arithmetic](#sra-shift-right-arithmetic)
  - [SRAI: Shift right arithmetic immediate](#srai-shift-right-arithmetic-immediate)
  - [SRL: Shift right logical](#srl-shift-right-logical)
  - [SRLI: Shift right logical immediate](#srli-shift-right-logical-immediate)
  - [SUB: Subtract](#sub-subtract)
  - [SUBI: Subtract immediate](#subi-subtract-immediate)
  - [SW: Store word](#sw-store-word)
  - [SYSCALL: System call](#syscall-system-call)
  - [XOR: XOR](#xor-xor)
  - [XORI: XOR immediate](#xori-xor-immediate)
- [Combination Arithmetic Operands](#combination-arithmetic-operands)
  - [ADDMOD: Addition then modulus](#addmod-addition-then-modulus)
  - [MULMOD: Multiply then modulus](#mulmod-multiply-then-modulus)
- [Ethereum-style Operands](#ethereum-style-operands)
  - [GAS:](#gas)
  - [GASLIMIT:](#gaslimit)
  - [GASPRICE:](#gasprice)
  - [BALANCE:](#balance)
  - [ADDRESS:](#address)
  - [ORIGIN:](#origin)
  - [CALLER:](#caller)
  - [CALLVALUE:](#callvalue)
  - [BLOCKHASH:](#blockhash)
  - [COINBASE:](#coinbase)
  - [TIMESTAMP:](#timestamp)
  - [NUMBER:](#number)
  - [DIFFICULTY:](#difficulty)
  - [CALLDATASIZE:](#calldatasize)
  - [CALLDATALOAD:](#calldataload)
  - [CALLDATACOPY:](#calldatacopy)
  - [CODESIZE:](#codesize)
  - [CODECOPY:](#codecopy)
  - [EXTCODESIZE:](#extcodesize)
  - [EXTCODEHASH:](#extcodehash)
  - [EXTCODECOPY:](#extcodecopy)
  - [RETURNDATASIZE:](#returndatasize)
  - [RETURNDATACOPY:](#returndatacopy)
  - [CALL:](#call)
  - [CALLCODE:](#callcode)
  - [DELEGATECALL:](#delegatecall)
  - [STATICCALL:](#staticcall)
  - [RETURN:](#return)
  - [CREATE2:](#create2)
  - [REVERT:](#revert)
  - [KECCAK: keccak-256](#keccak-keccak-256)
  - [LOG0:](#log0)
  - [LOG1:](#log1)
  - [LOG2:](#log2)
  - [LOG3:](#log3)
  - [LOG4:](#log4)

## Arithmetic/Logic (ALU) Operands

### ADD: Add

|             |                                                         |
| ----------- | ------------------------------------------------------- |
| Description | Adds two registers and stores the result in a register. |
| Operation   | ```$rd = $rs + $rt;```<br>```advance_pc();```           |
| Syntax      | `add $rd, $rs, $rt`                                     |
| Encoding    | `00000001 rd rs rt -`                                   |
| Notes       | `$of` is assigned the overflow of the operation.        |

### ADDI: Add immediate

|             |                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------ |
| Description | Adds a register and an immediate value and stores the result in a register.                |
| Operation   | ```$rt = $rs + imm;```<br>```advance_pc();```                                              |
| Syntax      | `addi $rd, $rs, immediate`                                                                 |
| Encoding    | `00000010 rs rt i i`                                                                       |
| Notes       | Immediate value sign extended to 64 bits. `$of` is assigned the overflow of the operation. |

### AND: AND

|             |                                                                 |
| ----------- | --------------------------------------------------------------- |
| Description | Bitwise ANDs two registers and stores the result in a register. |
| Operation   | ```$rd = $rs & $rt;```<br>```advance_pc();```                   |
| Syntax      | `and $rd, $rs, $rt`                                             |
| Encoding    | `00000101 rd rs rt -`                                           |
| Notes       | `$of` is cleared.                                               |

### ANDI: AND immediate

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Bitwise ANDs a register and an immediate value and stores the result in a register. |
| Operation   | ```$rd = $rs & imm;```<br>```advance_pc();```                                       |
| Syntax      | `andi $rd, $rs, imm`                                                                |
| Encoding    | `00000110 rd rs i i`                                                                |
| Notes       | Immediate value not sign extended. `$of` is cleared.                                |

### CPSR: Copy from special register

|             |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| Description | The contents of special register `$rs` are moved to the specified register. |
| Operation   | ```$rd = $rs;```<br>```advance_pc();```                                     |
| Syntax      | `cpsr $rd, $rs`                                                             |
| Encoding    | `00010111 rd rs - -`                                                        |
| Notes       |                                                                             |

### DIV: Divide

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Divides `$rs` by `$rt` and stores the quotient in `$rd` and the remainder in `$of`. |
| Operation   | ```$rd = $rs / $rt;```<br>```$of = $rs % $rt;```<br>```advance_pc();```             |
| Syntax      | `div $rd, $rs, $rt`                                                                 |
| Encoding    | `00001111 rd rs rt -`                                                               |
| Notes       |                                                                                     |

### DIVI: Divide immediate

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Divides `$rs` by `imm` and stores the quotient in `$rd` and the remainder in `$of`. |
| Operation   | ```$rd = $rs / imm;```<br>```$of = $rs % imm;```<br>```advance_pc();```             |
| Syntax      | `divi $rd, $rs, imm`                                                                |
| Encoding    | `00010000 rd rs rt -`                                                               |
| Notes       | Immediate value sign extended to 64 bits.                                           |

### EQ: Equals

|             |                                                   |
| ----------- | ------------------------------------------------- |
| Description | Assigns if two registers are equal to a register. |
| Operation   | ```$rd = $rs == $rt;```<br>```advance_pc();```    |
| Syntax      | `eq $rd, $rs, $rt`                                |
| Encoding    | `00000111 rd rs rt -`                             |
| Notes       |                                                   |

### EXP: Exponentiate

|             |                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt` values, then sets `$rd` to the result of `$rs` taken to the power of `$rt`. |
| Operation   | ```$rd = $rs ** $rt;```<br>```advance_pc();```                                                    |
| Syntax      | `exp $rd, $rs, $rt`                                                                               |
| Encoding    | `00110011 rd rs rt -`                                                                             |
| Notes       |                                                                                                   |

### EXPI: Exponentiate immediate

|             |                                                                                        |
| ----------- | -------------------------------------------------------------------------------------- |
| Description | Takes `$rs` value, then sets `$rd` to the result of `$rs` taken to the power of `imm`. |
| Operation   | ```$rd = $rs ** imm;```<br>```advance_pc();```                                         |
| Syntax      | `expi $rd, $rs, imm`                                                                   |
| Encoding    | `00110110 rd rt i i`                                                                   |
| Notes       |                                                                                        |

### GT: Greater than

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Assigns if a register is greater than another register to a register. |
| Operation   | ```$rd = $rs > $rd;```<br>```advance_pc();```                         |
| Syntax      | `gt $rd, $rs, $rd`                                                    |
| Encoding    | `00001010 rd rs rt -`                                                 |
| Notes       |                                                                       |

### J: Jump

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Jump to the address contained in register `$rs`. |
| Operation   | ```$pc = $rs;```                                 |
| Syntax      | `j $rs`                                          |
| Encoding    | `00010011 rs - - -`                              |
| Notes       |                                                  |

### JI: Jump immediate

|             |                       |
| ----------- | --------------------- |
| Description | Jumps to the address. |
| Operation   | ```$pc = imm;```      |
| Syntax      | `ji imm`              |
| Encoding    | `00010001 i i i i`    |
| Notes       |                       |

### LB: Load byte

|             |                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Description | A byte is loaded into a register from the specified address at the offset given by the immediate value. |
| Operation   | ```$rd = MEM[$rs + offset];```<br>```advance_pc();```                                                   |
| Syntax      | `lb $rd, $rs, offset`                                                                                   |
| Encoding    | `00010100 rd rs i i`                                                                                    |
| Notes       |                                                                                                         |

### LW: Load word

|             |                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Description | A word is loaded into a register from the specified address at the offset given by the immediate value. |
| Operation   | ```$rd = MEM[$rs + offset];```<br>```advance_pc();```                                                   |
| Syntax      | `lw $rd, $rs, offset`                                                                                   |
| Encoding    | `00010110 rd rs i i`                                                                                    |
| Notes       |                                                                                                         |

### MOD: Modulus

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Modulo remainder of `$rs` by `$rt` and sets `$rd` to result. |
| Operation   | ```$rd = $rs % $rt;```<br>```advance_pc();```                |
| Syntax      | `mod $rd, $rs, $rt`                                          |
| Encoding    | `00101111 rd rs rt -`                                        |
| Notes       |                                                              |

### MODI: Modulus immediate

|             |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| Description | Modulo remainder of `$rs` using immediate value and places result in `$rd`. |
| Operation   | ```$rd = $rs % imm;```<br>```advance_pc();```                               |
| Syntax      | `modi $rd, $rs, imm`                                                        |
| Encoding    | `00110100 rd rs i i`                                                        |
| Notes       |                                                                             |

### MUL: Multiply

|             |                                                                     |
| ----------- | ------------------------------------------------------------------- |
| Description | Multiplies `$rs` by `$rt` and stores the result in `$rd` and `$of`. |
| Operation   | ```$rd = $rs * $rt;```<br>```advance_pc();```                       |
| Syntax      | `mul $rd, $rs, $rt`                                                 |
| Encoding    | `00011010 rd rs rt -`                                               |
| Notes       | `$of` is assigned the overflow of the operation.                    |

### NOOP: No operation

|             |                        |
| ----------- | ---------------------- |
| Description | Performs no operation. |
| Operation   | ```advance_pc();```    |
| Syntax      | `noop`                 |
| Encoding    | `00011011 - - - -`     |
| Notes       |                        |

### OR: OR

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Bitwise ORs registers `$rs` and `$rt` and stores the result in `$rd`. |
| Operation   | ```$rd = $rs | $rt;```<br>```advance_pc();```                         |
| Syntax      | `or $rd, $rs, $rt`                                                    |
| Encoding    | `00011100 rd rs rt -`                                                 |
| Notes       | `$of` is cleared.                                                     |

### ORI: OR immediate

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Bitwise ORs register `$rs` and an immediate value and stores the result in `$rd`. |
| Operation   | ```$rd = $rs | imm;```<br>```advance_pc();```                                     |
| Syntax      | `ori $rd, $rs, imm`                                                               |
| Encoding    | `00011101 rd rs i i`                                                              |
| Notes       | `$of` is cleared.                                                                 |

### SB: Store byte

|             |                                                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------------------------- |
| Description | The least significant byte of `$rt` is stored at the specified address at an offset given by the immediate value. |
| Operation   | ```MEM[$rs + offset] = (0xff & $rt);```<br>```advance_pc();```                                                    |
| Syntax      | `sb $rt, $rs, offset`                                                                                             |
| Encoding    | `00011110 rs rt i i`                                                                                              |
| Notes       |                                                                                                                   |

### SLL: Shift left logical

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Shifts `$rs` left by `$rt` and places the result in `$rd`. Zeroes are shifted in. |
| Operation   | ```$rd = $rs << $rt;```<br>```advance_pc(); ```                                   |
| Syntax      | `sll $rd, $rs, $rt`                                                               |
| Encoding    | `00100000 rd rs rt -`                                                             |
| Notes       |                                                                                   |

### SLLI: Shift left logical immediate

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Shifts `$rs` left by `imm` and places the result in `$rd`. Zeroes are shifted in. |
| Operation   | ```$rd = $rs << imm;```<br>```advance_pc(); ```                                   |
| Syntax      | `slli $rd, $rs, imm`                                                              |
| Encoding    | `00011111 rd rs i i`                                                              |
| Notes       |                                                                                   |

### SRA: Shift right arithmetic

|             |                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------- |
| Description | Shifts a register value right by the amount specified in `$rs` and places the value in the destination register. |
| Operation   | ```$rd = $rs >> $rt;```<br>```advance_pc();```                                                                   |
| Syntax      | `sra $rd, $rs, $rt`                                                                                              |
| Encoding    | `00101000 rd rs rt -`                                                                                            |
| Notes       | The sign bit is shifted in.                                                                                      |

### SRAI: Shift right arithmetic immediate

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Shifts a register value right by the shift amount (imm) and places the value in the destination register. |
| Operation   | ```$rd = $rs >> imm;```<br>```advance_pc();```                                                            |
| Syntax      | `srai $rd, $rs, imm`                                                                                      |
| Encoding    | `00100101 rd rs i i`                                                                                      |
| Notes       | The sign bit is shifted in.                                                                               |

### SRL: Shift right logical

|             |                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------- |
| Description | Shifts a register value right by the amount specified in `$rs` and places the value in the destination register. |
| Operation   | ```$rd = $rs >> $rt;```<br>```advance_pc();```                                                                   |
| Syntax      | `srl $rd, $rs, $rt`                                                                                              |
| Encoding    | `00100111 rd rs rt -`                                                                                            |
| Notes       | Zeroes are shifted in.                                                                                           |

### SRLI: Shift right logical immediate

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Shifts a register value right by the shift amount `imm` and places the value in the destination register. |
| Operation   | ```$rd = $rs >> imm;```<br>```advance_pc();```                                                            |
| Syntax      | `srli $rd, $rs, imm`                                                                                      |
| Encoding    | `00100110 rd rs i i`                                                                                      |
| Notes       | Zeroes are shifted in.                                                                                    |

### SUB: Subtract

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Subtracts two registers and stores the result in a register. |
| Operation   | ```$rd = $rs - $rt;```<br>```advance_pc();```                |
| Syntax      | `sub $rd, $rs, $rt`                                          |
| Encoding    | `00101001 rd rs rt -`                                        |
| Notes       | `$of` is assigned the overflow of the operation.             |

### SUBI: Subtract immediate

|             |                                                                                  |
| ----------- | -------------------------------------------------------------------------------- |
| Description | Subtracts a register and an immediate value and stores the result in a register. |
| Operation   | ```$rd = $rs - $rt;```<br>```advance_pc();```                                    |
| Syntax      | `subi $rd, $rs, $rt`                                                             |
| Encoding    | `00101010 rd rs rt -`                                                            |
| Notes       | `$of` is assigned the overflow of the operation.                                 |

### SW: Store word

|             |                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Description | The contents of `$rt` is stored at the specified address using the offset given by the immediate value. |
| Operation   | ```MEM[$rs + offset] = $rt;```<br>```advance_pc();```                                                   |
| Syntax      | `sw $rt, $rs, offset`                                                                                   |
| Encoding    | `00101011 rs rt i i`                                                                                    |
| Notes       |                                                                                                         |

### SYSCALL: System call

|             |                          |
| ----------- | ------------------------ |
| Description | Noop for the time being. |
| Operation   | ```advance_pc();```      |
| Syntax      | `syscall`                |
| Encoding    | `00101100 - - - -`       |
| Notes       |                          |

### XOR: XOR

|             |                                                                 |
| ----------- | --------------------------------------------------------------- |
| Description | Bitwise XORs two registers and stores the result in a register. |
| Operation   | ```$rd = $rs ^ $rt;```<br>```advance_pc();```                   |
| Syntax      | `xor $rd, $rs, $rt `                                            |
| Encoding    | `00101101 rd rs rt -`                                           |
| Notes       | `$of` is cleared.                                               |

### XORI: XOR immediate

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Bitwise XORs a register and an immediate value and stores the result in a register. |
| Operation   | ```$rd = $rs ^ imm;```<br>```advance_pc();```                                       |
| Syntax      | `xori $rt, $rs, imm `                                                               |
| Encoding    | `00101110 rs rt i i`                                                                |
| Notes       | Immediate value not sign extended. `$of` is cleared.                                |

## Combination Arithmetic Operands

### ADDMOD: Addition then modulus

|             |                                                                                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt`, adds them, then takes the modulo of that result with `$ru`. Sets the `$rd` register value to the result. |
| Operation   | ```$rd = ($rs + $rt) % $ru```<br>```advance_pc();```                                                                            |
| Syntax      | `addmod $rd, $rs, $rt, $ru`                                                                                                     |
| Encoding    | `00110001 rd rs rt ru`                                                                                                          |
| Notes       |                                                                                                                                 |

### MULMOD: Multiply then modulus

|             |                                                                                                                                   |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt`, multiplies them, then takes the modulo of that result with `$ru`. Sets `$rd` register value to the result. |
| Operation   | ```$rd = ($rs * $rt) % $ru```<br>```advance_pc();```                                                                              |
| Syntax      | `mulmod $rd, $rs, $rt, $ru`                                                                                                       |
| Encoding    | `00110010 rd rs rt ru`                                                                                                            |
| Notes       |                                                                                                                                   |

## Ethereum-style Operands

### GAS: 

|             |                                                                    |
| ----------- | ------------------------------------------------------------------ |
| Description | Sets the $rd register value to the numeric value of gas remaining. |
| Operation   | ```$rd = $gas;```<br>```advance_pc();```                           |
| Syntax      | `gas $rd`                                                          |
| Encoding    | `10000000 rd - - - -`                                              |
| Notes       | EVM component                                                      |

### GASLIMIT: 

|             |                                                                          |
| ----------- | ------------------------------------------------------------------------ |
| Description | Sets $rd register value to the block's gas limit (as set by the client). |
| Operation   | ```$rd = $gaslimit;```<br>```advance_pc();```                            |
| Syntax      | `gaslimit $rd`                                                           |
| Encoding    | `10001011 rd - - - -`                                                    |
| Notes       | EVM component                                                            |

### GASPRICE: 

|             |                                                                                           |
| ----------- | ----------------------------------------------------------------------------------------- |
| Description | Sets the $rd register value to the gas price that was set in the originating transaction. |
| Operation   | ```$rd = $gasprice;```<br>```advance_pc();```                                             |
| Syntax      | `gasprice $rd`                                                                            |
| Encoding    | `10001100 rd - - - -`                                                                     |
| Notes       | EVM component                                                                             |

### BALANCE: 

|             |                                                                                                                          |
| ----------- | ------------------------------------------------------------------------------------------------------------------------ |
| Description | Takes the $rs register value, which is an address, and then sets the $rd register value to the balance of given address. |
| Operation   | ```$rd = $balance[$rs];```<br>```advance_pc();```                                                                        |
| Syntax      | `balance $rd, $rs`                                                                                                       |
| Encoding    | `10000001 rd rs - - -`                                                                                                   |
| Notes       | EVM component                                                                                                            |

### ADDRESS: 

|             |                                                                                |
| ----------- | ------------------------------------------------------------------------------ |
| Description | Sets the $rd register value to the address of the currently executing account. |
| Operation   | ```$rd = $address;```<br>```advance_pc();```                                   |
| Syntax      | `address $rd`                                                                  |
| Encoding    | `10000010 rd - - - -`                                                          |
| Notes       | EVM component                                                                  |

### ORIGIN: 

|             |                                                                                                                                                                           |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Sets the $rd register value to the execution origin address. This is the sender of the original transaction. Note: it is never an account with non-empty associated code. |
| Operation   | ```$rd = $origin;```<br>```advance_pc();```                                                                                                                               |
| Syntax      | `origin $rd`                                                                                                                                                              |
| Encoding    | `10000011 rd - - - -`                                                                                                                                                     |
| Notes       | EVM component                                                                                                                                                             |

### CALLER: 

|             |                                                                                                            |
| ----------- | ---------------------------------------------------------------------------------------------------------- |
| Description | Sets the $rd register value to the address of the account that is directly responsible for this execution. |
| Operation   | ```$rd = $caller;```<br>```advance_pc();```                                                                |
| Syntax      | `caller $rd`                                                                                               |
| Encoding    | `10000100 rd - - - -`                                                                                      |
| Notes       | EVM component                                                                                              |

### CALLVALUE: 

|             |                                                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------------------------- |
| Description | Sets the $rd register value to the value deposited by the instruction/transaction responsible for this execution. |
| Operation   | ```$rd = $value;```<br>```advance_pc();```                                                                        |
| Syntax      | `callvalue $rd`                                                                                                   |
| Encoding    | `10000101 rd - - - -`                                                                                             |
| Notes       | EVM component                                                                                                     |

### BLOCKHASH: 

|             |                                                                                                                                                               |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes 64-bit block number value from memory at address given by register $rs, and sets $rd register value to the hash of the block that at that block number. |
| Operation   | ```$rd = $blockhash(MEM[$rs..$rs+8]);```<br>```advance_pc();```                                                                                               |
| Syntax      | `blockhash $rd, $rs`                                                                                                                                          |
| Encoding    | `10000110 rd rs - - -`                                                                                                                                        |
| Notes       | EVM component                                                                                                                                                 |

### COINBASE: 

|             |                                                                            |
| ----------- | -------------------------------------------------------------------------- |
| Description | Sets $rd register value to the beneficiary address (as set by the client). |
| Operation   | ```$rd = $coinbase;```<br>```advance_pc();```                              |
| Syntax      | `coinbase $rd`                                                             |
| Encoding    | `10000111 rd - - - -`                                                      |
| Notes       | EVM component                                                              |

### TIMESTAMP: 

|             |                                                                          |
| ----------- | ------------------------------------------------------------------------ |
| Description | Sets $rd register value to the block's timestamp (as set by the client). |
| Operation   | ```$rd = $timestamp;```<br>```advance_pc();```                           |
| Syntax      | `timestamp $rd`                                                          |
| Encoding    | `10001000 rd - - - -`                                                    |
| Notes       | EVM component                                                            |

### NUMBER: 

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Sets $rd register value to the block's number (as set by the client). |
| Operation   | ```$rd = $number;```<br>```advance_pc();```                           |
| Syntax      | `number $rd`                                                          |
| Encoding    | `10001001 rd - - - -`                                                 |
| Notes       | EVM component                                                         |

### DIFFICULTY: 

|             |                                                                           |
| ----------- | ------------------------------------------------------------------------- |
| Description | Sets $rd register value to the block's difficulty (as set by the client). |
| Operation   | ```$rd = $difficulty;```<br>```advance_pc();```                           |
| Syntax      | `difficulty $rd`                                                          |
| Encoding    | `10001010 rd - - - -`                                                     |
| Notes       | EVM component                                                             |

### CALLDATASIZE: 

|             |                                                                         |
| ----------- | ----------------------------------------------------------------------- |
| Description | Sets the $rd register value to the size of a message call's input data. |
| Operation   | ```$rd = size($calldata);```<br>```advance_pc();```                     |
| Syntax      | `calldatasize $rd`                                                      |
| Encoding    | `10001101 rd - - - -`                                                   |
| Notes       | EVM component                                                           |

### CALLDATALOAD: 

|             |                                                                                                                                                                                     |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes the $rs register value, which represents the byte offset of a message call's input data, and sets the $rd register value to the 256-bit value at that offset of the calldata. |
| Operation   | ```$rd = $calldata[$rs];```<br>```advance_pc();```                                                                                                                                  |
| Syntax      | `calldataload $rd, $rs`                                                                                                                                                             |
| Encoding    | `10001110 rd rs - - -`                                                                                                                                                              |
| Notes       | EVM component                                                                                                                                                                       |

### CALLDATACOPY: 

|             |                                                                                                                                                                                                             |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Copy calldata into memory. The $rs value represents the offset in memory to start copying values from the call data which is offset by the $rd value. The number of bytes copied is equal to the $rt value. |
| Operation   | ```MEM[$rd] = $calldata[$rs..$rs+$rt];```<br>```advance_pc();```                                                                                                                                            |
| Syntax      | `calldatacopy $rd, $rs, $rt`                                                                                                                                                                                |
| Encoding    | `10001111 rd rs rt - -`                                                                                                                                                                                     |
| Notes       | EVM component                                                                                                                                                                                               |

### CODESIZE: 

|             |                                                                     |
| ----------- | ------------------------------------------------------------------- |
| Description | Sets the $rd register value to the size of the code being executed. |
| Operation   | ```$rd = $codesize($address);```<br>```advance_pc();```             |
| Syntax      | `codesize $rd`                                                      |
| Encoding    | `10010000 rd - - - -`                                               |
| Notes       | EVM component                                                       |

### CODECOPY: 

|             |                                                                                                                                                                                                                                  |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Copy code currently being executed into memory. The $rs value represents the offset in memory to start copying values from the code data which is $rd by the second value. The number of bytes copied is equal to the $rt value. |
| Operation   | ```MEM[$rd] = $code[$rs..$rs+$rt];```<br>```advance_pc();```                                                                                                                                                                     |
| Syntax      | `codecopy $rd, $rs, $rt`                                                                                                                                                                                                         |
| Encoding    | `10010001 rd rs rt - -`                                                                                                                                                                                                          |
| Notes       | EVM component                                                                                                                                                                                                                    |

### EXTCODESIZE: 

|             |                                                                                                                            |
| ----------- | -------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes the $rs register value, an account address, and sets the $rd register value to the size of the code at that address. |
| Operation   | ```$rd = $codesize($rs);```<br>```advance_pc();```                                                                         |
| Syntax      | `extcodesize $rd, $rs`                                                                                                     |
| Encoding    | `10010010 rd rs - - -`                                                                                                     |
| Notes       | EVM component                                                                                                              |

### EXTCODEHASH: 

|             |                                                                                                                               |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes $rs register value, the address, and sets $rd register value to the hash of the code at that address, else set it to 0. |
| Operation   | ```$rd = $codehash[$rs];```<br>```advance_pc();```                                                                            |
| Syntax      | `extcodehash $rd, $rs`                                                                                                        |
| Encoding    | `10010011 rd rs - - -`                                                                                                        |
| Notes       | EVM component                                                                                                                 |

### EXTCODECOPY: 

|             |                                                                                             |
| ----------- | ------------------------------------------------------------------------------------------- |
| Description | Like CODECOPY, but the $ru parameter for this operation is the account to copy the code of. |
| Operation   | ```MEM[$rd] = $code[$ru][$rs..$rs+$rt];```<br>```advance_pc();```                           |
| Syntax      | `extcodecopy $rd, $ru, $rs, $rt`                                                            |
| Encoding    | `10010100 rd rs rt ru - -`                                                                  |
| Notes       | EVM component                                                                               |

### RETURNDATASIZE: 

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Sets the $rd register value to the size of the returned data from the last call in the current execution. |
| Operation   | ```$rd = size($returndata);```<br>```advance_pc();```                                                     |
| Syntax      | `returndatasize $rd`                                                                                      |
| Encoding    | `10010101 rd - - - -`                                                                                     |
| Notes       | EVM component                                                                                             |

### RETURNDATACOPY: 

|             |                                                                                                                                                                                                                       |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Copies the return data into memory. $rs value specifies the address offset of the return data, the $rd value specifies the offset in memory to copy the data to, and the $rt value gives the number of bytes to copy. |
| Operation   | ```MEM[$rd] = $returndata[$rs..$rs+$rt];```<br>```advance_pc();```                                                                                                                                                    |
| Syntax      | `returndatacopy $rd, $rs, $rt`                                                                                                                                                                                        |
| Encoding    | `10010110 rd rs rt - -`                                                                                                                                                                                               |
| Notes       | EVM component                                                                                                                                                                                                         |

### CALL: 

|             |                                                                                                                                                                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Message call into an account where $rs points to a sequence of words in memory that are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the $rd register's value if the call completes successfully. |
| Operation   | ```pre-compile```                                                                                                                                                                                                                                        |
| Syntax      | `call $rd, $rs`                                                                                                                                                                                                                                          |
| Encoding    | `10010111 rd rs - - -`                                                                                                                                                                                                                                   |
| Notes       | EVM component                                                                                                                                                                                                                                            |

### CALLCODE: 

|             |                                                                                                                                                                                                                                                                                          |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Message call into an account with the current account's code where $rs points to a sequence of words in memory that are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the $rd register's value if the call completes successfully. |
| Operation   | ```pre-compile```                                                                                                                                                                                                                                                                        |
| Syntax      | `callcode $rd, $rs`                                                                                                                                                                                                                                                                      |
| Encoding    | `10011000 rd rs - - -`                                                                                                                                                                                                                                                                   |
| Notes       | EVM component                                                                                                                                                                                                                                                                            |

### DELEGATECALL: 

|             |                                                                                                                            |
| ----------- | -------------------------------------------------------------------------------------------------------------------------- |
| Description | Message-call into this account with an alternative account's code, but persisting the current values for sender and value. |
| Operation   | ```pre-compile```                                                                                                          |
| Syntax      | `delegatecall $rd, $rs`                                                                                                    |
| Encoding    | `10011001 rd rs - - -`                                                                                                     |
| Notes       | EVM component                                                                                                              |

### STATICCALL: 

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Static message-call into an account. |
| Operation   | ```pre-compile```                    |
| Syntax      | `staticcall $rd, $rs`                |
| Encoding    | `10011010 rd rs - - -`               |
| Notes       | EVM component                        |

### RETURN: 

|             |                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Description | Returns values from memory using first register value as memory offset whose size is given by the second register value. Program execution then halts. |
| Operation   | ```pre-compile```                                                                                                                                      |
| Syntax      | `return $rs, $rt`                                                                                                                                      |
| Encoding    | `10011011 rs rt - - -`                                                                                                                                 |
| Notes       | EVM component                                                                                                                                          |

### CREATE2: 

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Create a new account, like CREATE, but use the salt of the fourth register value. |
| Operation   | ```pre-compile```                                                                 |
| Syntax      | `create2 $rd, $rs, $rt, $ru, $rv`                                                 |
| Encoding    | `10011100 rd rs rt ru rv -`                                                       |
| Notes       | EVM component                                                                     |

### REVERT: 

|             |                                                                              |
| ----------- | ---------------------------------------------------------------------------- |
| Description | Halt execution reverting state changes but returning data and remaining gas. |
| Operation   | ```pre-compile```                                                            |
| Syntax      | `revert $rs, $rt`                                                            |
| Encoding    | `10011101 rs rt - - -`                                                       |
| Notes       | EVM component                                                                |

### KECCAK: keccak-256

|             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Keccak-256 hash. Takes $rs and $rt register values, values which represent the range of at least one item in memory to compute the Keccak-256 hash of. The $rs value is inclusive and the $rt value is exclusive; this means that the $rt value must be greater than the $rs value. The $rd register value is set to the hash value. Note: that this opcode mnemonic differs from the SHA3 opcode as specified in the yellow paper because SHA3 is not the same as Keccak. We do this to allow proper SHA3 operations in the Fuel VM. |
| Operation   | ```$rd = keccak(MEM[$rs:$rt]);```<br>```advance_pc(); ```                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Syntax      | `keccak $rd, $rs, $rt`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Encoding    | `10011110 rd rs rt - -`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Notes       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |

### LOG0: 

|             |                                                                                                                                                             |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes $rs and $rt register values and records a log entry with no topics based on the memory offset of the $rs value, with a length given by the $rt value. |
| Operation   | ```$log.push(':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();```                                                                                            |
| Syntax      | `log0 $rs, $rt`                                                                                                                                             |
| Encoding    | `10011111 rs rt - - -`                                                                                                                                      |
| Notes       | EVM component                                                                                                                                               |

### LOG1: 

|             |                                                                                                                                                                                                      |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru value. |
| Operation   | ```$log.push($ru + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();```                                                                                                                               |
| Syntax      | `log1 $rs, $rt, $ru`                                                                                                                                                                                 |
| Encoding    | `10100000 rs rt ru - -`                                                                                                                                                                              |
| Notes       | EVM component                                                                                                                                                                                        |

### LOG2: 

|             |                                                                                                                                                                                                            |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv values. |
| Operation   | ```$log.push($ru + $rv + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();```                                                                                                                               |
| Syntax      | `log2 $rs, $rt, $ru, $rv`                                                                                                                                                                                  |
| Encoding    | `10100001 rs rt ru rv - -`                                                                                                                                                                                 |
| Notes       | EVM component                                                                                                                                                                                              |

### LOG3: 

|             |                                                                                                                                                                                                                 |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw values. |
| Operation   | ```$log.push($ru + $rv + $rw + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();```                                                                                                                              |
| Syntax      | `log3 $rs, $rt, $ru, $rv, $rw`                                                                                                                                                                                  |
| Encoding    | `10100010 rs rt ru rv rw -`                                                                                                                                                                                     |
| Notes       | EVM component                                                                                                                                                                                                   |

### LOG4: 

|             |                                                                                                                                                                                                                      |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw, $rx values. |
| Operation   | ```$log.push($ru + $rv + $rw + $rx + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();```                                                                                                                             |
| Syntax      | `log4 $rs, $rt, $ru, $rv, $rw, $rx`                                                                                                                                                                                  |
| Encoding    | `10100011 rs rt ru rv rw rx`                                                                                                                                                                                         |
| Notes       | EVM component                                                                                                                                                                                                        |
