# FuelVM Opcodes

- [Arithmetic/Logic (ALU) Opcodes](#arithmeticlogic-alu-opcodes)
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
  - [MOD: Modulus](#mod-modulus)
  - [MODI: Modulus immediate](#modi-modulus-immediate)
  - [MUL: Multiply](#mul-multiply)
  - [NOOP: No operation](#noop-no-operation)
  - [OR: OR](#or-or)
  - [ORI: OR immediate](#ori-or-immediate)
  - [SLL: Shift left logical](#sll-shift-left-logical)
  - [SLLI: Shift left logical immediate](#slli-shift-left-logical-immediate)
  - [SRL: Shift right logical](#srl-shift-right-logical)
  - [SRLI: Shift right logical immediate](#srli-shift-right-logical-immediate)
  - [SUB: Subtract](#sub-subtract)
  - [SUBI: Subtract immediate](#subi-subtract-immediate)
  - [XOR: XOR](#xor-xor)
  - [XORI: XOR immediate](#xori-xor-immediate)
- [Combination Arithmetic Opcodes](#combination-arithmetic-opcodes)
  - [ADDMOD: Addition then modulus](#addmod-addition-then-modulus)
  - [MULMOD: Multiply then modulus](#mulmod-multiply-then-modulus)
- [Control Flow Opcodes](#control-flow-opcodes)
  - [J: Jump](#j-jump)
  - [JI: Jump immediate](#ji-jump-immediate)
- [Memory Opcodes](#memory-opcodes)
  - [LB: Load byte](#lb-load-byte)
  - [LW: Load word](#lw-load-word)
  - [SB: Store byte](#sb-store-byte)
  - [SW: Store word](#sw-store-word)
- [Contract Opcodes](#contract-opcodes)
  - [CALL: Call contract](#call-call-contract)
  - [CODECOPY: Code copy](#codecopy-code-copy)
  - [CODEROOT: Code Merkle root](#coderoot-code-merkle-root)
  - [CODESIZE: Code size](#codesize-code-size)
  - [CREATE: Create contract](#create-create-contract)
  - [GAS: Remaining gas](#gas-remaining-gas)
  - [KECCAK256: keccak-256](#keccak256-keccak-256)
  - [LOG: Log event](#log-log-event)
  - [RETURN: Return from call](#return-return-from-call)
  - [REVERT: Revert](#revert-revert)
  - [SRW: State read word](#srw-state-read-word)
  - [SRWX: State read 32 bytes](#srwx-state-read-32-bytes)
  - [SWW: State write word](#sww-state-write-word)
  - [SWWX: State write 32 bytes](#swwx-state-write-32-bytes)

## Arithmetic/Logic (ALU) Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### ADD: Add

|             |                                                         |
| ----------- | ------------------------------------------------------- |
| Description | Adds two registers and stores the result in a register. |
| Operation   | ```$rd = $rs + $rt;```                                  |
| Syntax      | `add $rd, $rs, $rt`                                     |
| Encoding    | `00000001 rd rs rt -`                                   |
| Notes       | `$of` is assigned the overflow of the operation.        |

### ADDI: Add immediate

|             |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| Description | Adds a register and an immediate value and stores the result in a register. |
| Operation   | ```$rt = $rs + imm;```                                                      |
| Syntax      | `addi $rd, $rs, immediate`                                                  |
| Encoding    | `00000010 rs rt i i`                                                        |
| Notes       | `$of` is assigned the overflow of the operation.                            |

### AND: AND

|             |                                                                 |
| ----------- | --------------------------------------------------------------- |
| Description | Bitwise ANDs two registers and stores the result in a register. |
| Operation   | ```$rd = $rs & $rt;```                                          |
| Syntax      | `and $rd, $rs, $rt`                                             |
| Encoding    | `00000101 rd rs rt -`                                           |
| Notes       | `$of` is cleared.                                               |

### ANDI: AND immediate

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Bitwise ANDs a register and an immediate value and stores the result in a register. |
| Operation   | ```$rd = $rs & imm;```                                                              |
| Syntax      | `andi $rd, $rs, imm`                                                                |
| Encoding    | `00000110 rd rs i i`                                                                |
| Notes       | `$of` is cleared.                                                                   |

### CPSR: Copy from special register

|             |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| Description | The contents of special register `$rs` are moved to the specified register. |
| Operation   | ```$rd = $rs;```                                                            |
| Syntax      | `cpsr $rd, $rs`                                                             |
| Encoding    | `00010111 rd rs - -`                                                        |
| Notes       |                                                                             |

### DIV: Divide

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Divides `$rs` by `$rt` and stores the quotient in `$rd` and the remainder in `$of`. |
| Operation   | ```$rd = $rs / $rt;```<br>```$of = $rs % $rt;```                                    |
| Syntax      | `div $rd, $rs, $rt`                                                                 |
| Encoding    | `00001111 rd rs rt -`                                                               |
| Notes       |                                                                                     |

### DIVI: Divide immediate

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Divides `$rs` by `imm` and stores the quotient in `$rd` and the remainder in `$of`. |
| Operation   | ```$rd = $rs / imm;```<br>```$of = $rs % imm;```                                    |
| Syntax      | `divi $rd, $rs, imm`                                                                |
| Encoding    | `00010000 rd rs rt -`                                                               |
| Notes       |                                                                                     |

### EQ: Equals

|             |                                                   |
| ----------- | ------------------------------------------------- |
| Description | Assigns if two registers are equal to a register. |
| Operation   | ```$rd = $rs == $rt;```                           |
| Syntax      | `eq $rd, $rs, $rt`                                |
| Encoding    | `00000111 rd rs rt -`                             |
| Notes       |                                                   |

### EXP: Exponentiate

|             |                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt` values, then sets `$rd` to the result of `$rs` taken to the power of `$rt`. |
| Operation   | ```$rd = $rs ** $rt;```                                                                           |
| Syntax      | `exp $rd, $rs, $rt`                                                                               |
| Encoding    | `00110011 rd rs rt -`                                                                             |
| Notes       |                                                                                                   |

### EXPI: Exponentiate immediate

|             |                                                                                        |
| ----------- | -------------------------------------------------------------------------------------- |
| Description | Takes `$rs` value, then sets `$rd` to the result of `$rs` taken to the power of `imm`. |
| Operation   | ```$rd = $rs ** imm;```                                                                |
| Syntax      | `expi $rd, $rs, imm`                                                                   |
| Encoding    | `00110110 rd rt i i`                                                                   |
| Notes       |                                                                                        |

### GT: Greater than

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Assigns if a register is greater than another register to a register. |
| Operation   | ```$rd = $rs > $rt;```                                                |
| Syntax      | `gt $rd, $rs, $rt`                                                    |
| Encoding    | `00001010 rd rs rt -`                                                 |
| Notes       |                                                                       |

### MOD: Modulus

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Modulo remainder of `$rs` by `$rt` and sets `$rd` to result. |
| Operation   | ```$rd = $rs % $rt;```                                       |
| Syntax      | `mod $rd, $rs, $rt`                                          |
| Encoding    | `00101111 rd rs rt -`                                        |
| Notes       |                                                              |

### MODI: Modulus immediate

|             |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| Description | Modulo remainder of `$rs` using immediate value and places result in `$rd`. |
| Operation   | ```$rd = $rs % imm;```                                                      |
| Syntax      | `modi $rd, $rs, imm`                                                        |
| Encoding    | `00110100 rd rs i i`                                                        |
| Notes       |                                                                             |

### MUL: Multiply

|             |                                                                     |
| ----------- | ------------------------------------------------------------------- |
| Description | Multiplies `$rs` by `$rt` and stores the result in `$rd` and `$of`. |
| Operation   | ```$rd = $rs * $rt;```                                              |
| Syntax      | `mul $rd, $rs, $rt`                                                 |
| Encoding    | `00011010 rd rs rt -`                                               |
| Notes       | `$of` is assigned the overflow of the operation.                    |

### NOOP: No operation

|             |                        |
| ----------- | ---------------------- |
| Description | Performs no operation. |
| Operation   |                        |
| Syntax      | `noop`                 |
| Encoding    | `00011011 - - - -`     |
| Notes       |                        |

### OR: OR

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Bitwise ORs registers `$rs` and `$rt` and stores the result in `$rd`. |
| Operation   | ```$rd = $rs | $rt;```                                                |
| Syntax      | `or $rd, $rs, $rt`                                                    |
| Encoding    | `00011100 rd rs rt -`                                                 |
| Notes       | `$of` is cleared.                                                     |

### ORI: OR immediate

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Bitwise ORs register `$rs` and an immediate value and stores the result in `$rd`. |
| Operation   | ```$rd = $rs | imm;```                                                            |
| Syntax      | `ori $rd, $rs, imm`                                                               |
| Encoding    | `00011101 rd rs i i`                                                              |
| Notes       | `$of` is cleared.                                                                 |

### SLL: Shift left logical

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Shifts `$rs` left by `$rt` and places the result in `$rd`. Zeroes are shifted in. |
| Operation   | ```$rd = $rs << $rt;```                                                           |
| Syntax      | `sll $rd, $rs, $rt`                                                               |
| Encoding    | `00100000 rd rs rt -`                                                             |
| Notes       |                                                                                   |

### SLLI: Shift left logical immediate

|             |                                                                                   |
| ----------- | --------------------------------------------------------------------------------- |
| Description | Shifts `$rs` left by `imm` and places the result in `$rd`. Zeroes are shifted in. |
| Operation   | ```$rd = $rs << imm;```                                                           |
| Syntax      | `slli $rd, $rs, imm`                                                              |
| Encoding    | `00011111 rd rs i i`                                                              |
| Notes       |                                                                                   |

### SRL: Shift right logical

|             |                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------- |
| Description | Shifts a register value right by the amount specified in `$rs` and places the value in the destination register. |
| Operation   | ```$rd = $rs >> $rt;```                                                                                          |
| Syntax      | `srl $rd, $rs, $rt`                                                                                              |
| Encoding    | `00100111 rd rs rt -`                                                                                            |
| Notes       | Zeroes are shifted in.                                                                                           |

### SRLI: Shift right logical immediate

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Shifts a register value right by the shift amount `imm` and places the value in the destination register. |
| Operation   | ```$rd = $rs >> imm;```                                                                                   |
| Syntax      | `srli $rd, $rs, imm`                                                                                      |
| Encoding    | `00100110 rd rs i i`                                                                                      |
| Notes       | Zeroes are shifted in.                                                                                    |

### SUB: Subtract

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Subtracts two registers and stores the result in a register. |
| Operation   | ```$rd = $rs - $rt;```                                       |
| Syntax      | `sub $rd, $rs, $rt`                                          |
| Encoding    | `00101001 rd rs rt -`                                        |
| Notes       | `$of` is assigned the overflow of the operation.             |

### SUBI: Subtract immediate

|             |                                                                                  |
| ----------- | -------------------------------------------------------------------------------- |
| Description | Subtracts a register and an immediate value and stores the result in a register. |
| Operation   | ```$rd = $rs - $rt;```                                                           |
| Syntax      | `subi $rd, $rs, $rt`                                                             |
| Encoding    | `00101010 rd rs rt -`                                                            |
| Notes       | `$of` is assigned the overflow of the operation.                                 |

### XOR: XOR

|             |                                                                 |
| ----------- | --------------------------------------------------------------- |
| Description | Bitwise XORs two registers and stores the result in a register. |
| Operation   | ```$rd = $rs ^ $rt;```                                          |
| Syntax      | `xor $rd, $rs, $rt `                                            |
| Encoding    | `00101101 rd rs rt -`                                           |
| Notes       | `$of` is cleared.                                               |

### XORI: XOR immediate

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | Bitwise XORs a register and an immediate value and stores the result in a register. |
| Operation   | ```$rd = $rs ^ imm;```                                                              |
| Syntax      | `xori $rt, $rs, imm `                                                               |
| Encoding    | `00101110 rs rt i i`                                                                |
| Notes       | `$of` is cleared.                                                                   |

## Combination Arithmetic Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### ADDMOD: Addition then modulus

|             |                                                                                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt`, adds them, then takes the modulo of that result with `$ru`. Sets the `$rd` register value to the result. |
| Operation   | ```$rd = ($rs + $rt) % $ru```                                                                                                   |
| Syntax      | `addmod $rd, $rs, $rt, $ru`                                                                                                     |
| Encoding    | `00110001 rd rs rt ru`                                                                                                          |
| Notes       |                                                                                                                                 |

### MULMOD: Multiply then modulus

|             |                                                                                                                                   |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt`, multiplies them, then takes the modulo of that result with `$ru`. Sets `$rd` register value to the result. |
| Operation   | ```$rd = ($rs * $rt) % $ru```                                                                                                     |
| Syntax      | `mulmod $rd, $rs, $rt, $ru`                                                                                                       |
| Encoding    | `00110010 rd rs rt ru`                                                                                                            |
| Notes       |                                                                                                                                   |

## Control Flow Opcodes

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

## Memory Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### LB: Load byte

|             |                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Description | A byte is loaded into a register from the specified address at the offset given by the immediate value. |
| Operation   | ```$rd = MEM[$rs + offset, 1];```                                                                       |
| Syntax      | `lb $rd, $rs, offset`                                                                                   |
| Encoding    | `00010100 rd rs i i`                                                                                    |
| Notes       |                                                                                                         |

### LW: Load word

|             |                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Description | A word is loaded into a register from the specified address at the offset given by the immediate value. |
| Operation   | ```$rd = MEM[$rs + offset, 8];```                                                                       |
| Syntax      | `lw $rd, $rs, offset`                                                                                   |
| Encoding    | `00010110 rd rs i i`                                                                                    |
| Notes       |                                                                                                         |

### SB: Store byte

|             |                                                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------------------------- |
| Description | The least significant byte of `$rt` is stored at the specified address at an offset given by the immediate value. |
| Operation   | ```MEM[$rs + offset, 1] = $rt[0, 1];```                                                                           |
| Syntax      | `sb $rt, $rs, offset`                                                                                             |
| Encoding    | `00011110 rs rt i i`                                                                                              |
| Notes       |                                                                                                                   |

### SW: Store word

|             |                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Description | The contents of `$rt` is stored at the specified address using the offset given by the immediate value. |
| Operation   | ```MEM[$rs + offset, 8] = $rt;```                                                                       |
| Syntax      | `sw $rt, $rs, offset`                                                                                   |
| Encoding    | `00101011 rs rt i i`                                                                                    |
| Notes       |                                                                                                         |

## Contract Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### CALL: Call contract

|             |                                                                                                                                                                                                                                                                |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Message call into an account where `$rs` points to a sequence of words in memory that are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value `1` is set as the `$rd` register's value if the call completes successfully. |
| Operation   |                                                                                                                                                                                                                                                                |
| Syntax      | `call $rd, $rs`                                                                                                                                                                                                                                                |
| Encoding    | `10010111 rd rs - -`                                                                                                                                                                                                                                           |
| Notes       |                                                                                                                                                                                                                                                                |

### CODECOPY: Code copy

|             |                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Description | Copy `$ru` bytes of code starting at `$rt` for contract with ID equal to the 32 bytes in memory starting at `$rs` into memory starting at `$rd`. |
| Operation   | ```MEM[$rd, $ru] = code($rs, $rt, $ru);```                                                                                                       |
| Syntax      | `codecopy $rs, $rs, $rt, $ru`                                                                                                                    |
| Encoding    | `10010100 rd rs rt ru`                                                                                                                           |
| Notes       | If `$rt` is greater than the code size, zero bytes are filled in.                                                                                |

### CODEROOT: Code Merkle root

|             |                                                                                                                                              |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Set the 32 bytes in memory starting at `$rd` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = coderoot(MEM[$rs, 32]);```                                                                                                 |
| Syntax      | `codehash $rd, $rs`                                                                                                                          |
| Encoding    | `10010011 rd rs - -`                                                                                                                         |
| Notes       |                                                                                                                                              |

### CODESIZE: Code size

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Set `$rd` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rs`. |
| Operation   | ```$rd = codesize(MEM[$rs, 32]);```                                                                       |
| Syntax      | `codesize $rd, $rs`                                                                                       |
| Encoding    | `10010010 rd rs - -`                                                                                      |
| Notes       |                                                                                                           |

### CREATE: Create contract

|             |                                                                    |
| ----------- | ------------------------------------------------------------------ |
| Description | Create a new account, using the salt of the fourth register value. |
| Operation   |                                                                    |
| Syntax      | `create $rd`                                                       |
| Encoding    | `10011100 rd - - -`                                                |
| Notes       |                                                                    |

### GAS: Remaining gas

|             |                                                       |
| ----------- | ----------------------------------------------------- |
| Description | Sets the `$rd` to the numeric value of gas remaining. |
| Operation   | ```$rd = $gas;```                                     |
| Syntax      | `gas $rd`                                             |
| Encoding    | `10000000 rd - - -`                                   |
| Notes       |                                                       |

### KECCAK256: keccak-256

|             |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| Description | The keccak-256 hash of `$rt` bytes starting at `$rs` are assigned to `$rd`. |
| Operation   | ```$rd = keccak(MEM[$rs, $rt]);```                                          |
| Syntax      | `keccak $rd, $rs, $rt`                                                      |
| Encoding    | `10011110 rd rs rt -`                                                       |
| Notes       |                                                                             |

### LOG: Log event

|             |                                                                                                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt` register values and records a log entry with no topics based on the memory offset of the `$rs` value, with a length given by the `$rt` value. |
| Operation   | ```$log.push(':' + MEM[$rs, $rt]);```                                                                                                                               |
| Syntax      | `log $rs, $rt`                                                                                                                                                      |
| Encoding    | `10011111 rs rt - -`                                                                                                                                                |
| Notes       |                                                                                                                                                                     |

### RETURN: Return from call

|             |                                                                                                                          |
| ----------- | ------------------------------------------------------------------------------------------------------------------------ |
| Description | Returns values from memory using first register value as memory offset whose size is given by the second register value. |
| Operation   |                                                                                                                          |
| Syntax      | `return $rs, $rt`                                                                                                        |
| Encoding    | `10011011 rs rt - -`                                                                                                     |
| Notes       |                                                                                                                          |

### REVERT: Revert

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Halt execution, reverting state changes and returning value in `$rs`. |
| Operation   | ```revert $rs;```                                                     |
| Syntax      | `revert $rs`                                                          |
| Encoding    | `10011101 rs - - -`                                                   |
| Notes       |                                                                       |

### SRW: State read word

|             |                                                   |
| ----------- | ------------------------------------------------- |
| Description | A word is read from the current contract's state. |
| Operation   | ```$rd = STATE[MEM[$rs, 32]][0, 8];```            |
| Syntax      | `srw $rd, $rs`                                    |
| Encoding    | `00010100 rd rs - -`                              |
| Notes       | Returns zero if the state element does not exist. |

### SRWX: State read 32 bytes

|             |                                                     |
| ----------- | --------------------------------------------------- |
| Description | 32 bytes is read from the current contract's state. |
| Operation   | ```MEM[$rd, 32] = STATE[MEM[$rs, 32]];```           |
| Syntax      | `srwx $rd, $rs`                                     |
| Encoding    | `00010100 rd rs - -`                                |
| Notes       | Returns zero if the state element does not exist.   |

### SWW: State write word

|             |                                                    |
| ----------- | -------------------------------------------------- |
| Description | A word is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rd, 32]][0, 8] = $rs;```             |
| Syntax      | `sww $rd $rs`                                      |
| Encoding    | `00010100 rd rs - -`                               |
| Notes       |                                                    |

### SWWX: State write 32 bytes

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | 32 bytes is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rd, 32]] = MEM[$rs, 32];```            |
| Syntax      | `swwx $rd, $rs`                                      |
| Encoding    | `00010100 rd rs - -`                                 |
| Notes       |                                                      |
