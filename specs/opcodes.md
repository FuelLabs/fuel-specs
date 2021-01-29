# FuelVM Opcodes

- [Reading Guide](#reading-guide)
- [Arithmetic/Logic (ALU) Opcodes](#arithmeticlogic-alu-opcodes)
  - [ADD: Add](#add-add)
  - [ADDI: Add immediate](#addi-add-immediate)
  - [AND: AND](#and-and)
  - [ANDI: AND immediate](#andi-and-immediate)
  - [DIV: Divide](#div-divide)
  - [DIVI: Divide immediate](#divi-divide-immediate)
  - [EQ: Equals](#eq-equals)
  - [EXP: Exponentiate](#exp-exponentiate)
  - [EXPI: Exponentiate immediate](#expi-exponentiate-immediate)
  - [GT: Greater than](#gt-greater-than)
  - [MOD: Modulus](#mod-modulus)
  - [MODI: Modulus immediate](#modi-modulus-immediate)
  - [MUL: Multiply](#mul-multiply)
  - [MULI: Multiply immediate](#muli-multiply-immediate)
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
- [Control Flow Opcodes](#control-flow-opcodes)
  - [CIMV: Check input maturity verify](#cimv-check-input-maturity-verify)
  - [CTMV: Check transaction maturity verify](#ctmv-check-transaction-maturity-verify)
  - [HALT: Halt](#halt-halt)
  - [J: Jump](#j-jump)
  - [JI: Jump immediate](#ji-jump-immediate)
  - [JNZ: Jump if not zero](#jnz-jump-if-not-zero)
  - [JNZI: Jump if not zero immediate](#jnzi-jump-if-not-zero-immediate)
- [Memory Opcodes](#memory-opcodes)
  - [CFE: Extend call frame](#cfe-extend-call-frame)
  - [CFS: Shrink call frame](#cfs-shrink-call-frame)
  - [LB: Load byte](#lb-load-byte)
  - [LW: Load word](#lw-load-word)
  - [MALLOC: Allocate memory](#malloc-allocate-memory)
  - [MEMEQ: Memory equality](#memeq-memory-equality)
  - [MEMCP: Memory copy](#memcp-memory-copy)
  - [SB: Store byte](#sb-store-byte)
  - [SW: Store word](#sw-store-word)
- [Contract Opcodes](#contract-opcodes)
  - [BLOCKHASH: Block hash](#blockhash-block-hash)
  - [BLOCKHEIGHT: Block height](#blockheight-block-height)
  - [CALL: Call contract](#call-call-contract)
  - [CODECOPY: Code copy](#codecopy-code-copy)
  - [CODEROOT: Code Merkle root](#coderoot-code-merkle-root)
  - [CODESIZE: Code size](#codesize-code-size)
  - [COINBASE](#coinbase)
  - [CREATE: Create contract](#create-create-contract)
  - [LOG: Log event](#log-log-event)
  - [RETURN: Return from call](#return-return-from-call)
  - [REVERT: Revert](#revert-revert)
  - [SRW: State read word](#srw-state-read-word)
  - [SRWX: State read 32 bytes](#srwx-state-read-32-bytes)
  - [SWW: State write word](#sww-state-write-word)
  - [SWWX: State write 32 bytes](#swwx-state-write-32-bytes)
- [Cryptographic Opcodes](#cryptographic-opcodes)
  - [ECRECOVER: Signature recovery](#ecrecover-signature-recovery)
  - [KECCAK256: keccak-256](#keccak256-keccak-256)
  - [SHA256: SHA-2-256](#sha256-sha-2-256)

## Reading Guide

This page provides a description of all opcodes for the FuelVM. Encoding should be read as a sequence of one 8-bit value (the opcode identifier) followed by four 6-bit values (the register identifiers or immediate value). A single `i` indicates a 6-bit immediate value, `i i` indicates a 12-bit immediate value and  `i i i` indicates a 24-bit immediate value. All immediate values are interpreted as big-endian unsigned integers.

## Arithmetic/Logic (ALU) Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### ADD: Add

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Adds two registers.                              |
| Operation   | ```$rd = $rs + $rt;```                           |
| Syntax      | `add $rd, $rs, $rt`                              |
| Encoding    | `0x00 rd rs rt -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

### ADDI: Add immediate

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Adds a register and an immediate value.          |
| Operation   | ```$rt = $rs + imm;```                           |
| Syntax      | `addi $rd, $rs, immediate`                       |
| Encoding    | `0x00 rs rt i i`                                 |
| Notes       | `$of` is assigned the overflow of the operation. |

### AND: AND

|             |                             |
| ----------- | --------------------------- |
| Description | Bitwise ANDs two registers. |
| Operation   | ```$rd = $rs & $rt;```      |
| Syntax      | `and $rd, $rs, $rt`         |
| Encoding    | `0x00 rd rs rt -`           |
| Notes       | `$of` is cleared.           |

### ANDI: AND immediate

|             |                                                 |
| ----------- | ----------------------------------------------- |
| Description | Bitwise ANDs a register and an immediate value. |
| Operation   | ```$rd = $rs & imm;```                          |
| Syntax      | `andi $rd, $rs, imm`                            |
| Encoding    | `0x00 rd rs i i`                                |
| Notes       | `$of` is cleared.                               |

### DIV: Divide

|             |                                                          |
| ----------- | -------------------------------------------------------- |
| Description | Divides two registers.                                   |
| Operation   | ```$rd = $rs / $rt;```<br>```$of = $rs % $rt;```         |
| Syntax      | `div $rd, $rs, $rt`                                      |
| Encoding    | `0x00 rd rs rt -`                                        |
| Notes       | Stores the quotient in `$rd` and the remainder in `$of`. |

### DIVI: Divide immediate

|             |                                                         |
| ----------- | ------------------------------------------------------- |
| Description | Divides a register and an immediate value.              |
| Operation   | ```$rd = $rs / imm;```<br>```$of = $rs % imm;```        |
| Syntax      | `divi $rd, $rs, imm`                                    |
| Encoding    | `0x00 rd rs rt -`                                       |
| Notes       | Stores the quotient in `$rd` and the remainder in `$of` |

### EQ: Equals

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Compares two registers for equality. |
| Operation   | ```$rd = $rs == $rt;```              |
| Syntax      | `eq $rd, $rs, $rt`                   |
| Encoding    | `0x00 rd rs rt -`                    |
| Notes       |                                      |

### EXP: Exponentiate

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | Raises one register to the power of another. |
| Operation   | ```$rd = $rs ** $rt;```                      |
| Syntax      | `exp $rd, $rs, $rt`                          |
| Encoding    | `0x00 rd rs rt -`                            |
| Notes       |                                              |

### EXPI: Exponentiate immediate

|             |                                                         |
| ----------- | ------------------------------------------------------- |
| Description | Raises one register to the power of an immediate value. |
| Operation   | ```$rd = $rs ** imm;```                                 |
| Syntax      | `expi $rd, $rs, imm`                                    |
| Encoding    | `0x00 rd rt i i`                                        |
| Notes       |                                                         |

### GT: Greater than

|             |                                          |
| ----------- | ---------------------------------------- |
| Description | Compares two registers for greater-than. |
| Operation   | ```$rd = $rs > $rt;```                   |
| Syntax      | `gt $rd, $rs, $rt`                       |
| Encoding    | `0x00 rd rs rt -`                        |
| Notes       |                                          |

### MOD: Modulus

|             |                                    |
| ----------- | ---------------------------------- |
| Description | Modulo remainder of two registers. |
| Operation   | ```$rd = $rs % $rt;```             |
| Syntax      | `mod $rd, $rs, $rt`                |
| Encoding    | `0x00 rd rs rt -`                  |
| Notes       |                                    |

### MODI: Modulus immediate

|             |                                                        |
| ----------- | ------------------------------------------------------ |
| Description | Modulo remainder of a register and an immediate value. |
| Operation   | ```$rd = $rs % imm;```                                 |
| Syntax      | `modi $rd, $rs, imm`                                   |
| Encoding    | `0x00 rd rs i i`                                       |
| Notes       |                                                        |

### MUL: Multiply

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Multiplies two registers.                        |
| Operation   | ```$rd = $rs * $rt;```                           |
| Syntax      | `mul $rd, $rs, $rt`                              |
| Encoding    | `0x00 rd rs rt -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

### MULI: Multiply immediate

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Multiplies a register and an immediate value.    |
| Operation   | ```$rd = $rs * imm;```                           |
| Syntax      | `mul $rd, $rs, imm`                              |
| Encoding    | `0x00 rd rs i i`                                 |
| Notes       | `$of` is assigned the overflow of the operation. |

### NOOP: No operation

|             |                        |
| ----------- | ---------------------- |
| Description | Performs no operation. |
| Operation   |                        |
| Syntax      | `noop`                 |
| Encoding    | `0x00 - - - -`         |
| Notes       |                        |

### OR: OR

|             |                            |
| ----------- | -------------------------- |
| Description | Bitwise ORs two registers. |
| Operation   | ```$rd = $rs | $rt;```     |
| Syntax      | `or $rd, $rs, $rt`         |
| Encoding    | `0x00 rd rs rt -`          |
| Notes       | `$of` is cleared.          |

### ORI: OR immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Bitwise ORs a register and an immediate value. |
| Operation   | ```$rd = $rs | imm;```                         |
| Syntax      | `ori $rd, $rs, imm`                            |
| Encoding    | `0x00 rd rs i i`                               |
| Notes       | `$of` is cleared.                              |

### SLL: Shift left logical

|             |                                       |
| ----------- | ------------------------------------- |
| Description | Left shifts a register by a register. |
| Operation   | ```$rd = $rs << $rt;```               |
| Syntax      | `sll $rd, $rs, $rt`                   |
| Encoding    | `0x00 rd rs rt -`                     |
| Notes       | Zeroes are shifted in.                |

### SLLI: Shift left logical immediate

|             |                                               |
| ----------- | --------------------------------------------- |
| Description | Left shifts a register by an immediate value. |
| Operation   | ```$rd = $rs << imm;```                       |
| Syntax      | `slli $rd, $rs, imm`                          |
| Encoding    | `0x00 rd rs i i`                              |
| Notes       | Zeroes are shifted in.                        |

### SRL: Shift right logical

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Right shifts a register by a register. |
| Operation   | ```$rd = $rs >> $rt;```                |
| Syntax      | `srl $rd, $rs, $rt`                    |
| Encoding    | `0x00 rd rs rt -`                      |
| Notes       | Zeroes are shifted in.                 |

### SRLI: Shift right logical immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Right shifts a register by an immediate value. |
| Operation   | ```$rd = $rs >> imm;```                        |
| Syntax      | `srli $rd, $rs, imm`                           |
| Encoding    | `0x00 rd rs i i`                               |
| Notes       | Zeroes are shifted in.                         |

### SUB: Subtract

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Subtracts two registers.                         |
| Operation   | ```$rd = $rs - $rt;```                           |
| Syntax      | `sub $rd, $rs, $rt`                              |
| Encoding    | `0x00 rd rs rt -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

### SUBI: Subtract immediate

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Subtracts a register and an immediate value.     |
| Operation   | ```$rd = $rs - $rt;```                           |
| Syntax      | `subi $rd, $rs, $rt`                             |
| Encoding    | `0x00 rd rs rt -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

### XOR: XOR

|             |                             |
| ----------- | --------------------------- |
| Description | Bitwise XORs two registers. |
| Operation   | ```$rd = $rs ^ $rt;```      |
| Syntax      | `xor $rd, $rs, $rt `        |
| Encoding    | `0x00 rd rs rt -`           |
| Notes       | `$of` is cleared.           |

### XORI: XOR immediate

|             |                                                 |
| ----------- | ----------------------------------------------- |
| Description | Bitwise XORs a register and an immediate value. |
| Operation   | ```$rd = $rs ^ imm;```                          |
| Syntax      | `xori $rt, $rs, imm `                           |
| Encoding    | `0x00 rs rt i i`                                |
| Notes       | `$of` is cleared.                               |

## Control Flow Opcodes

### CIMV: Check input maturity verify

|             |                                                             |
| ----------- | ----------------------------------------------------------- |
| Description | Set `$rd` to `true` if the `$rt <= tx.input[$rs].maturity`. |
| Operation   | ```$rd = checkinputmaturityverify($rs, $rt);```             |
| Syntax      | `cimv $rd $rs $rt`                                          |
| Encoding    | `0x00 rd rs rt -`                                           |
| Notes       |                                                             |

If `$rt > tx.input[$rs].maturity`, [halt](#halt-halt), returning `false`. If the input `$rs` is not of type [`InputType.Coin`](./tx_format.md), [halt](#halt-halt), returning `false`.

See also: [BIP-112](https://github.com/bitcoin/bips/blob/master/bip-0112.mediawiki) and [CLTV](#cltv-check-lock-time-verify).

### CTMV: Check transaction maturity verify

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Set `$rd` to `true` if `$rs <= tx.maturity`.     |
| Operation   | ```$rd = checktransactionmaturityverify($rs);``` |
| Syntax      | `ctmv $rd $rs`                                   |
| Encoding    | `0x00 rd rs - -`                                 |
| Notes       |                                                  |

If `$rs > tx.maturity`, [halt](#halt-halt), returning `false`.

See also: [BIP-65](https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki) and [Bitcoin's Time Locks](https://prestwi.ch/bitcoin-time-locks).

### HALT: Halt

|             |                                                                         |
| ----------- | ----------------------------------------------------------------------- |
| Description | Halt execution, keeping any state changes and returning value in `$rs`. |
| Operation   | ```halt $rs;```                                                         |
| Syntax      | `halt $rs`                                                              |
| Encoding    | `0x00 rs - - -`                                                         |
| Notes       |                                                                         |

### J: Jump

|             |                            |
| ----------- | -------------------------- |
| Description | Jump to the address `$rs`. |
| Operation   | ```$pc = $rs;```           |
| Syntax      | `j $rs`                    |
| Encoding    | `0x00 rs - - -`            |
| Notes       |                            |

### JI: Jump immediate

|             |                                       |
| ----------- | ------------------------------------- |
| Description | Jumps to the immediate address `imm`. |
| Operation   | ```$pc = imm;```                      |
| Syntax      | `ji imm`                              |
| Encoding    | `0x00 i i i i`                        |
| Notes       |                                       |

### JNZ: Jump if not zero

|             |                                                                              |
| ----------- | ---------------------------------------------------------------------------- |
| Description | Jump to the address `$rs` if `$rt` is not zero.                              |
| Operation   | ```if $rt != 0:```<br>```  $pc = $rs;```<br>```else:```<br>```  $pc += 4;``` |
| Syntax      | `jnz $rs, $rt`                                                               |
| Encoding    | `0x00 rs rt - -`                                                             |
| Notes       |                                                                              |

### JNZI: Jump if not zero immediate

|             |                                                                              |
| ----------- | ---------------------------------------------------------------------------- |
| Description | Jump to the immediate address `imm` if `$rs` is not zero.                    |
| Operation   | ```if $rs != 0:```<br>```  $pc = imm;```<br>```else:```<br>```  $pc += 4;``` |
| Syntax      | `jnzi $rs`                                                                   |
| Encoding    | `0x00 rs i i i`                                                              |
| Notes       |                                                                              |

## Memory Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### CFE: Extend call frame

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Extend the current call frame's stack. |
| Operation   | ```$sp = $sp + $rs```                  |
| Syntax      | `cfe $rs`                              |
| Encoding    | `0x00 rs - - -`                        |
| Notes       | Does not initialize memory.            |

### CFS: Shrink call frame

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Shrink the current call frame's stack. |
| Operation   | ```$sp = $sp - $rs```                  |
| Syntax      | `cfs $rs`                              |
| Encoding    | `0x00 rs - - -`                        |
| Notes       | Does not clear memory.                 |

### LB: Load byte

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | A byte is loaded from the specified address offset by `imm`. |
| Operation   | ```$rd = MEM[$rs + imm, 1];```                               |
| Syntax      | `lb $rd, $rs, imm`                                           |
| Encoding    | `0x00 rd rs i i`                                             |
| Notes       |                                                              |

### LW: Load word

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | A word is loaded from the specified address offset by `imm`. |
| Operation   | ```$rd = MEM[$rs + imm, 8];```                               |
| Syntax      | `lw $rd, $rs, imm`                                           |
| Encoding    | `0x00 rd rs i i`                                             |
| Notes       |                                                              |

### MALLOC: Allocate memory

|             |                                           |
| ----------- | ----------------------------------------- |
| Description | Allocate a number of bytes from the heap. |
| Operation   | ```$hp = $hp - $rs;```                    |
| Syntax      | `malloc $rs`                              |
| Encoding    | `0x00 rs - - -`                           |
| Notes       | Does not initialize memory.               |

### MEMEQ: Memory equality

|             |                                             |
| ----------- | ------------------------------------------- |
| Description | Compare bytes in memory.                    |
| Operation   | ```$rd = MEM[$rs, $ru] == MEM[$rt, $ru];``` |
| Syntax      | `memeq $rd, $rs, $rt, $ru`                  |
| Encoding    | `0x00 rd rs rt ru`                          |
| Notes       |                                             |

### MEMCP: Memory copy

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rd, $rt] = MEM[$rs, $rt];``` |
| Syntax      | `memcp $rd, $rs, $rt`                |
| Encoding    | `0x00 rd rs rt -`                    |
| Notes       |                                      |

### SB: Store byte

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | The least significant byte of `$rs` is stored at the address `$rd` offset by `imm`. |
| Operation   | ```MEM[$rd + imm, 1] = $rs[7, 1];```                                                |
| Syntax      | `sb $rd, $rs, imm`                                                                  |
| Encoding    | `0x00 rd rs i i`                                                                    |
| Notes       |                                                                                     |

The memory range `MEM[$rd + imm, 1]` [is checked for ownership](./main.md#ownership). The check failing causes a revert, with this instruction consuming TODO gas.

### SW: Store word

|             |                                                                    |
| ----------- | ------------------------------------------------------------------ |
| Description | The value of `$rs` is stored at the address `$rd` offset by `imm`. |
| Operation   | ```MEM[$rd + imm, 8] = $rs;```                                     |
| Syntax      | `sw $rd, $rs, imm`                                                 |
| Encoding    | `0x00 rd rs i i`                                                   |
| Notes       |                                                                    |

The memory range `MEM[$rd + imm, 8]` [is checked for ownership](./main.md#ownership). The check failing causes a revert, with this instruction consuming TODO gas.

## Contract Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

### BLOCKHASH: Block hash

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Get block header hash.               |
| Operation   | ```MEM[$rd, 32] = blockhash($rs);``` |
| Syntax      | `blockhash $rd $rs`                  |
| Encoding    | `0x00 rd rs - -`                     |
| Notes       |                                      |

Block header hashes for blocks with height greater than or equal to current block height are zero (`0x00**32`).

### BLOCKHEIGHT: Block height

|             |                            |
| ----------- | -------------------------- |
| Description | Get Fuel block height.     |
| Operation   | ```$rd = blockheight();``` |
| Syntax      | `blockheight $rd`          |
| Encoding    | `0x00 rd - - -`            |
| Notes       |                            |

### CALL: Call contract

|             |                  |
| ----------- | ---------------- |
| Description | Call contract.   |
| Operation   |                  |
| Syntax      | `call $rd, $rs`  |
| Encoding    | `0x00 rd rs - -` |
| Notes       |                  |

Register `$rs` is a memory address from which the following fields are set (word-aligned):

| bytes | type                 | value             | description                                                      |
| ----- | -------------------- | ----------------- | ---------------------------------------------------------------- |
| 8     | `uint64`             | gas               | Amount of gas to forward.                                        |
| 32    | `byte[32]`           | to                | Contract ID to call.                                             |
| 1     | `uint8`              | out count         | Number of return values.                                         |
| 1     | `uint8`              | in count          | Number of input values.                                          |
| 16*   | `(uint32, uint32)[]` | out (addr, size)s | Array of memory addresses and lengths in bytes of return values. |
| 16*   | `(uint32, uint32)[]` | in (addr, size)s  | Array of memory addresses and lengths in bytes of input values.  |

If gas is set to an amount greater than the available gas, all available gas is forwarded.

Reading past `MEM[VM_MAX_RAM - 1]` causes a revert, with this instruction consuming TODO gas.

Each output range [is checked for ownership](./main.md#ownership). Any check failing causes a revert, with this instruction consuming TODO gas.

If the above checks pass, a [call frame](./main.md#call-frames) is pushed at `$sp`. In addition to filling in the values of the call frame, the following registers are set:
1. `$fp = $sp` (on top of the previous call frame is the beginning of this call frame)
1. `$sp = $fp + MEM[$fp + 0]` (first word is offset to free stack)
1. `$pc = $fp + MEM[$fp + 16]` (third word is code offset, pc is not advanced by 4)
1. `$gas` = forwarded gas.

### CODECOPY: Code copy

|             |                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Description | Copy `$ru` bytes of code starting at `$rt` for contract with ID equal to the 32 bytes in memory starting at `$rs` into memory starting at `$rd`. |
| Operation   | ```MEM[$rd, $ru] = code($rs, $rt, $ru);```                                                                                                       |
| Syntax      | `codecopy $rs, $rs, $rt, $ru`                                                                                                                    |
| Encoding    | `0x00 rd rs rt ru`                                                                                                                               |
| Notes       | If `$ru` is greater than the code size, zero bytes are filled in.                                                                                |

### CODEROOT: Code Merkle root

|             |                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Set the 32 bytes in memory starting at `$rd` to the code root for contract with ID equal to the 32 bytes in memory starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = coderoot(MEM[$rs, 32]);```                                                                                          |
| Syntax      | `codehash $rd, $rs`                                                                                                                   |
| Encoding    | `0x00 rd rs - -`                                                                                                                      |
| Notes       |                                                                                                                                       |

### CODESIZE: Code size

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Set `$rd` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rs`. |
| Operation   | ```$rd = codesize(MEM[$rs, 32]);```                                                                       |
| Syntax      | `codesize $rd, $rs`                                                                                       |
| Encoding    | `0x00 rd rs - -`                                                                                          |
| Notes       |                                                                                                           |

### COINBASE

|             |                                  |
| ----------- | -------------------------------- |
| Description | Get block proposer address.      |
| Operation   | ```MEM[$rd, 32] = coinbase();``` |
| Syntax      | `coinbase $rd`                   |
| Encoding    | `0x00 rd - - -`                  |
| Notes       |                                  |

### CREATE: Create contract

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Create a new contract with `$rt` bytes of bytecode starting at `$rs`. |
| Operation   | ```create(MEM[$rs, $rt])```                                           |
| Syntax      | `create $rs, $rt`                                                     |
| Encoding    | `0x00 rs rt - -`                                                      |
| Notes       |                                                                       |

### LOG: Log event

|             |                                                                                                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Takes `$rs` and `$rt` register values and records a log entry with no topics based on the memory offset of the `$rs` value, with a length given by the `$rt` value. |
| Operation   | ```$log.push(':' + MEM[$rs, $rt]);```                                                                                                                               |
| Syntax      | `log $rs, $rt`                                                                                                                                                      |
| Encoding    | `0x00 rs rt - -`                                                                                                                                                    |
| Notes       |                                                                                                                                                                     |

<!--TODO-->

### RETURN: Return from call

|             |                             |
| ----------- | --------------------------- |
| Description | Returns from contract call. |
| Operation   |                             |
| Syntax      | `return`                    |
| Encoding    | `0x00 - - - -`              |
| Notes       |                             |

Return from contract call, popping the call frame. Before popping, return the unused forwarded gas to the caller:
1. `$gas = $gas + MEM[$fp + 24]` (remaining gas from caller is 4th word)

Then pop the call frame and restoring registers _except_ the `$gas`. Afterwards, set the following registers:
1. `$pc = $pc + 4` (advance program counter from where we called)

### REVERT: Revert

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Halt execution, reverting state changes and returning value in `$rs`. |
| Operation   | ```revert $rs;```                                                     |
| Syntax      | `revert $rs`                                                          |
| Encoding    | `0x00 rs - - -`                                                       |
| Notes       |                                                                       |

### SRW: State read word

|             |                                                   |
| ----------- | ------------------------------------------------- |
| Description | A word is read from the current contract's state. |
| Operation   | ```$rd = STATE[MEM[$rs, 32]][0, 8];```            |
| Syntax      | `srw $rd, $rs`                                    |
| Encoding    | `0x00 rd rs - -`                                  |
| Notes       | Returns zero if the state element does not exist. |

### SRWX: State read 32 bytes

|             |                                                     |
| ----------- | --------------------------------------------------- |
| Description | 32 bytes is read from the current contract's state. |
| Operation   | ```MEM[$rd, 32] = STATE[MEM[$rs, 32]];```           |
| Syntax      | `srwx $rd, $rs`                                     |
| Encoding    | `0x00 rd rs - -`                                    |
| Notes       | Returns zero if the state element does not exist.   |

### SWW: State write word

|             |                                                    |
| ----------- | -------------------------------------------------- |
| Description | A word is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rd, 32]][0, 8] = $rs;```             |
| Syntax      | `sww $rd $rs`                                      |
| Encoding    | `0x00 rd rs - -`                                   |
| Notes       |                                                    |

### SWWX: State write 32 bytes

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | 32 bytes is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rd, 32]] = MEM[$rs, 32];```            |
| Syntax      | `swwx $rd, $rs`                                      |
| Encoding    | `0x00 rd rs - -`                                     |
| Notes       |                                                      |

## Cryptographic Opcodes

### ECRECOVER: Signature recovery

|             |                                                                                                                             |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- |
| Description | The 64-byte public key (x, y) recovered from 64-byte signature starting at `$rs` on 32-byte message hash starting at `$rt`. |
| Operation   | ```MEM[$rd, 64] = ecrecover(MEM[$rs, 64], MEM[$rt, 32]);```                                                                 |
| Syntax      | `ecrecover $rd, $rs, $rt`                                                                                                   |
| Encoding    | `0x00 rd rs rt -`                                                                                                           |
| Notes       |                                                                                                                             |

To get the address, hash the public key with [SHA-2-256](#sha256-sha-2-256).

### KECCAK256: keccak-256

|             |                                                       |
| ----------- | ----------------------------------------------------- |
| Description | The keccak-256 hash of `$rt` bytes starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = keccak256(MEM[$rs, $rt]);```        |
| Syntax      | `keccak256 $rd, $rs, $rt`                             |
| Encoding    | `0x00 rd rs rt -`                                     |
| Notes       |                                                       |

### SHA256: SHA-2-256

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | The SHA-2-256 hash of `$rt` bytes starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = sha256(MEM[$rs, $rt]);```          |
| Syntax      | `sha256 $rd, $rs, $rt`                               |
| Encoding    | `0x00 rd rs rt -`                                    |
| Notes       |                                                      |
