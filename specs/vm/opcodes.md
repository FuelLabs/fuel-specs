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
  - [MATHLOG: Math logarithm](#mathlog-math-logarithm)
  - [MATHROOT: Math root](#mathroot-math-root)
  - [MOD: Modulus](#mod-modulus)
  - [MODI: Modulus immediate](#modi-modulus-immediate)
  - [MUL: Multiply](#mul-multiply)
  - [MULI: Multiply immediate](#muli-multiply-immediate)
  - [NOOP: No operation](#noop-no-operation)
  - [NOT: Invert](#not-invert)
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
  - [JI: Jump immediate](#ji-jump-immediate)
  - [JNZI: Jump if not zero immediate](#jnzi-jump-if-not-zero-immediate)
  - [RETURN: Return from context](#return-return-from-context)
- [Memory Opcodes](#memory-opcodes)
  - [CFE: Extend call frame](#cfe-extend-call-frame)
  - [CFS: Shrink call frame](#cfs-shrink-call-frame)
  - [LB: Load byte](#lb-load-byte)
  - [LW: Load word](#lw-load-word)
  - [MALLOC: Allocate memory](#malloc-allocate-memory)
  - [MEMCLEAR: Memory clear](#memclear-memory-clear)
  - [MEMCP: Memory copy](#memcp-memory-copy)
  - [MEMEQ: Memory equality](#memeq-memory-equality)
  - [SB: Store byte](#sb-store-byte)
  - [SW: Store word](#sw-store-word)
- [Contract Opcodes](#contract-opcodes)
  - [BLOCKHASH: Block hash](#blockhash-block-hash)
  - [BLOCKHEIGHT: Block height](#blockheight-block-height)
  - [CALL: Call contract](#call-call-contract)
  - [CODECOPY: Code copy](#codecopy-code-copy)
  - [CODEROOT: Code Merkle root](#coderoot-code-merkle-root)
  - [CODESIZE: Code size](#codesize-code-size)
  - [COINBASE: Block proposer address](#coinbase-block-proposer-address)
  - [LOADCODE: Load code from an external contract](#loadcode-load-code-from-an-external-contract)
  - [LOG: Log event](#log-log-event)
  - [REVERT: Revert](#revert-revert)
  - [SRW: State read word](#srw-state-read-word)
  - [SRWX: State read 32 bytes](#srwx-state-read-32-bytes)
  - [SWW: State write word](#sww-state-write-word)
  - [SWWX: State write 32 bytes](#swwx-state-write-32-bytes)
  - [TRANSFER: Transfer coins](#transfer-transfer-coins)
- [Cryptographic Opcodes](#cryptographic-opcodes)
  - [ECRECOVER: Signature recovery](#ecrecover-signature-recovery)
  - [KECCAK256: keccak-256](#keccak256-keccak-256)
  - [SHA256: SHA-2-256](#sha256-sha-2-256)
- [Other Opcodes](#other-opcodes)
  - [FLAG: Set flags](#flag-set-flags)

## Reading Guide

This page provides a description of all opcodes for the FuelVM. Encoding should be read as a sequence of one 8-bit value (the opcode identifier) followed by four 6-bit values (the register identifiers or immediate value). A single `i` indicates a 6-bit immediate value, `i i` indicates a 12-bit immediate value, `i i i` indicates an 18-bit immediate value, and `i i i i` indicates a 24-bit immediate value. All immediate values are interpreted as big-endian unsigned integers.

Some opcodes may _panic_, i.e. enter an unrecoverable state. How a panic is handled depends on [context](./main.md#contexts):
* In a predicate context, [return](#return-return-from-context) `false`.
* In other contexts, [revert](#revert-revert).

Attempting to execute an opcode not in this list pauses a panic and consumes no gas.

## Arithmetic/Logic (ALU) Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation.

If the [`F_UNSAFEMATH`](./main.md#flags) flag is unset, an operation that would have set `$err` to `true` is instead a panic.

If the [`F_WRAPPING`](./main.md#flags) flag is unset, an operation that would have set `$of` to a non-zero value is instead a panic.

### ADD: Add

|             |                        |
| ----------- | ---------------------- |
| Description | Adds two registers.    |
| Operation   | ```$rd = $rs + $rt;``` |
| Syntax      | `add $rd, $rs, $rt`    |
| Encoding    | `0x00 rd rs rt -`      |
| Notes       |                        |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### ADDI: Add immediate

|             |                                         |
| ----------- | --------------------------------------- |
| Description | Adds a register and an immediate value. |
| Operation   | ```$rt = $rs + imm;```                  |
| Syntax      | `addi $rd, $rs, immediate`              |
| Encoding    | `0x00 rs rt i i`                        |
| Notes       |                                         |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### AND: AND

|             |                             |
| ----------- | --------------------------- |
| Description | Bitwise ANDs two registers. |
| Operation   | ```$rd = $rs & $rt;```      |
| Syntax      | `and $rd, $rs, $rt`         |
| Encoding    | `0x00 rd rs rt -`           |
| Notes       |                             |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### ANDI: AND immediate

|             |                                                 |
| ----------- | ----------------------------------------------- |
| Description | Bitwise ANDs a register and an immediate value. |
| Operation   | ```$rd = $rs & imm;```                          |
| Syntax      | `andi $rd, $rs, imm`                            |
| Encoding    | `0x00 rd rs i i`                                |
| Notes       |                                                 |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### DIV: Divide

|             |                         |
| ----------- | ----------------------- |
| Description | Divides two registers.  |
| Operation   | ```$rd = $rs // $rt;``` |
| Syntax      | `div $rd, $rs, $rt`     |
| Encoding    | `0x00 rd rs rt -`       |
| Notes       |                         |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If `$rt == 0`, `$rd` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### DIVI: Divide immediate

|             |                                            |
| ----------- | ------------------------------------------ |
| Description | Divides a register and an immediate value. |
| Operation   | ```$rd = $rs // imm;```                    |
| Syntax      | `divi $rd, $rs, imm`                       |
| Encoding    | `0x00 rd rs i i`                           |
| Notes       |                                            |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If `imm == 0`, `$rd` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### EQ: Equals

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Compares two registers for equality. |
| Operation   | ```$rd = $rs == $rt;```              |
| Syntax      | `eq $rd, $rs, $rt`                   |
| Encoding    | `0x00 rd rs rt -`                    |
| Notes       |                                      |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### EXP: Exponentiate

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | Raises one register to the power of another. |
| Operation   | ```$rd = $rs ** $rt;```                      |
| Syntax      | `exp $rd, $rs, $rt`                          |
| Encoding    | `0x00 rd rs rt -`                            |
| Notes       |                                              |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If the result cannot fit in 8 bits, `$of` is set to `1`, otherwise `$of` is cleared.

`$err` is cleared.

### EXPI: Exponentiate immediate

|             |                                                         |
| ----------- | ------------------------------------------------------- |
| Description | Raises one register to the power of an immediate value. |
| Operation   | ```$rd = $rs ** imm;```                                 |
| Syntax      | `expi $rd, $rs, imm`                                    |
| Encoding    | `0x00 rd rt i i`                                        |
| Notes       |                                                         |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If the result cannot fit in 8 bits, `$of` is set to `1`, otherwise `$of` is cleared.

`$err` is cleared.

### GT: Greater than

|             |                                          |
| ----------- | ---------------------------------------- |
| Description | Compares two registers for greater-than. |
| Operation   | ```$rd = $rs > $rt;```                   |
| Syntax      | `gt $rd, $rs, $rt`                       |
| Encoding    | `0x00 rd rs rt -`                        |
| Notes       |                                          |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### MATHLOG: Math logarithm

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | The (integer) logarithm base `$rt` of `$rs`. |
| Operation   | ```$rd = math.floor(math.log($rs, $rt));```  |
| Syntax      | `mathlog $rd, $rs, $rt`                      |
| Encoding    | `0x00 rd rs rt -`                            |
| Notes       |                                              |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If `$rs == 0`, both `$rd` and `$of` are cleared and `$err` is set to `true`.

If `$rt <= 1`, both `$rd` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MATHROOT: Math root

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | The (integer) `$rt`th root of `$rs`.         |
| Operation   | ```$rd = math.floor(math.root($rs, $rt));``` |
| Syntax      | `mathroot $rd, $rs, $rt`                     |
| Encoding    | `0x00 rd rs rt -`                            |
| Notes       |                                              |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If `$rt == 0`, both `$rd` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MOD: Modulus

|             |                                    |
| ----------- | ---------------------------------- |
| Description | Modulo remainder of two registers. |
| Operation   | ```$rd = $rs % $rt;```             |
| Syntax      | `mod $rd, $rs, $rt`                |
| Encoding    | `0x00 rd rs rt -`                  |
| Notes       |                                    |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If `$rt == 0`, both `$rd` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MODI: Modulus immediate

|             |                                                        |
| ----------- | ------------------------------------------------------ |
| Description | Modulo remainder of a register and an immediate value. |
| Operation   | ```$rd = $rs % imm;```                                 |
| Syntax      | `modi $rd, $rs, imm`                                   |
| Encoding    | `0x00 rd rs i i`                                       |
| Notes       |                                                        |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

If `imm == 0`, both `$rd` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MUL: Multiply

|             |                           |
| ----------- | ------------------------- |
| Description | Multiplies two registers. |
| Operation   | ```$rd = $rs * $rt;```    |
| Syntax      | `mul $rd, $rs, $rt`       |
| Encoding    | `0x00 rd rs rt -`         |
| Notes       |                           |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### MULI: Multiply immediate

|             |                                               |
| ----------- | --------------------------------------------- |
| Description | Multiplies a register and an immediate value. |
| Operation   | ```$rd = $rs * imm;```                        |
| Syntax      | `mul $rd, $rs, imm`                           |
| Encoding    | `0x00 rd rs i i`                              |
| Notes       |                                               |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### NOOP: No operation

|             |                        |
| ----------- | ---------------------- |
| Description | Performs no operation. |
| Operation   |                        |
| Syntax      | `noop`                 |
| Encoding    | `0x00 - - - -`         |
| Notes       |                        |

`$of` and `$err` are cleared.

### NOT: Invert

|             |                         |
| ----------- | ----------------------- |
| Description | Bitwise NOT a register. |
| Operation   | ```$rd = ~$rs;```       |
| Syntax      | `not $rd, $rs`          |
| Encoding    | `0x00 rd rs - -`        |
| Notes       |                         |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### OR: OR

|             |                            |
| ----------- | -------------------------- |
| Description | Bitwise ORs two registers. |
| Operation   | ```$rd = $rs | $rt;```     |
| Syntax      | `or $rd, $rs, $rt`         |
| Encoding    | `0x00 rd rs rt -`          |
| Notes       |                            |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### ORI: OR immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Bitwise ORs a register and an immediate value. |
| Operation   | ```$rd = $rs | imm;```                         |
| Syntax      | `ori $rd, $rs, imm`                            |
| Encoding    | `0x00 rd rs i i`                               |
| Notes       |                                                |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### SLL: Shift left logical

|             |                                       |
| ----------- | ------------------------------------- |
| Description | Left shifts a register by a register. |
| Operation   | ```$rd = $rs << $rt;```               |
| Syntax      | `sll $rd, $rs, $rt`                   |
| Encoding    | `0x00 rd rs rt -`                     |
| Notes       | Zeroes are shifted in.                |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### SLLI: Shift left logical immediate

|             |                                               |
| ----------- | --------------------------------------------- |
| Description | Left shifts a register by an immediate value. |
| Operation   | ```$rd = $rs << imm;```                       |
| Syntax      | `slli $rd, $rs, imm`                          |
| Encoding    | `0x00 rd rs i i`                              |
| Notes       | Zeroes are shifted in.                        |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### SRL: Shift right logical

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Right shifts a register by a register. |
| Operation   | ```$rd = $rs >> $rt;```                |
| Syntax      | `srl $rd, $rs, $rt`                    |
| Encoding    | `0x00 rd rs rt -`                      |
| Notes       | Zeroes are shifted in.                 |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SRLI: Shift right logical immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Right shifts a register by an immediate value. |
| Operation   | ```$rd = $rs >> imm;```                        |
| Syntax      | `srli $rd, $rs, imm`                           |
| Encoding    | `0x00 rd rs i i`                               |
| Notes       | Zeroes are shifted in.                         |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SUB: Subtract

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Subtracts two registers.                         |
| Operation   | ```$rd = $rs - $rt;```                           |
| Syntax      | `sub $rd, $rs, $rt`                              |
| Encoding    | `0x00 rd rs rt -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SUBI: Subtract immediate

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Subtracts a register and an immediate value.     |
| Operation   | ```$rd = $rs - $rt;```                           |
| Syntax      | `subi $rd, $rs, $rt`                             |
| Encoding    | `0x00 rd rs rt -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### XOR: XOR

|             |                             |
| ----------- | --------------------------- |
| Description | Bitwise XORs two registers. |
| Operation   | ```$rd = $rs ^ $rt;```      |
| Syntax      | `xor $rd, $rs, $rt `        |
| Encoding    | `0x00 rd rs rt -`           |
| Notes       |                             |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### XORI: XOR immediate

|             |                                                 |
| ----------- | ----------------------------------------------- |
| Description | Bitwise XORs a register and an immediate value. |
| Operation   | ```$rd = $rs ^ imm;```                          |
| Syntax      | `xori $rt, $rs, imm `                           |
| Encoding    | `0x00 rs rt i i`                                |
| Notes       |                                                 |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

## Control Flow Opcodes

### CIMV: Check input maturity verify

|             |                                                             |
| ----------- | ----------------------------------------------------------- |
| Description | Set `$rd` to `true` if the `$rt <= tx.input[$rs].maturity`. |
| Operation   | ```$rd = checkinputmaturityverify($rs, $rt);```             |
| Syntax      | `cimv $rd $rs $rt`                                          |
| Encoding    | `0x00 rd rs rt -`                                           |
| Notes       |                                                             |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
* `$rt > tx.input[$rs].maturity`
* the input `$rs` is not of type [`InputType.Coin`](../protocol/tx_format.md)
* `$rs > tx.inputsCount`

Otherwise, advance the program counter `$pc` by `4`.

See also: [BIP-112](https://github.com/bitcoin/bips/blob/master/bip-0112.mediawiki) and [CLTV](#cltv-check-lock-time-verify).

### CTMV: Check transaction maturity verify

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Set `$rd` to `true` if `$rs <= tx.maturity`.     |
| Operation   | ```$rd = checktransactionmaturityverify($rs);``` |
| Syntax      | `ctmv $rd $rs`                                   |
| Encoding    | `0x00 rd rs - -`                                 |
| Notes       |                                                  |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
* `$rs > tx.maturity`

Otherwise, advance the program counter `$pc` by `4`.

See also: [BIP-65](https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki) and [Bitcoin's Time Locks](https://prestwi.ch/bitcoin-time-locks).

### JI: Jump immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Jumps to the code instruction offset by `imm`. |
| Operation   | ```$pc = $is + imm * 4;```                     |
| Syntax      | `ji imm`                                       |
| Encoding    | `0x00 i i i i`                                 |
| Notes       |                                                |

Panic if:
* `$is + imm * 4 > VM_MAX_RAM - 1`

### JNZI: Jump if not zero immediate

|             |                                                                                        |
| ----------- | -------------------------------------------------------------------------------------- |
| Description | Jump to the code instruction offset by `imm` if `$rs` is not zero.                     |
| Operation   | ```if $rs != 0:```<br>```  $pc = $is + imm * 4;```<br>```else:```<br>```  $pc += 4;``` |
| Syntax      | `jnzi $rs`                                                                             |
| Encoding    | `0x00 rs i i i`                                                                        |
| Notes       |                                                                                        |

Panic if:
* `$is + imm * 4 > VM_MAX_RAM - 1`

### RETURN: Return from context

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Returns from [context](./main.md#contexts) with value `$rs`. |
| Operation   | ```return($rs);```                                           |
| Syntax      | `return $rs`                                                 |
| Encoding    | `0x00 rs - - -`                                              |
| Notes       |                                                              |

If current context is external, cease VM execution and return `$rs`.

Returns from contract call, popping the call frame. Before popping:
1. Return the unused forwarded gas to the caller:
    * `$cgas = $cgas + $fp->$cgas` (add remaining context gas from previous context to current remaining context gas)

Then pop the call frame and restoring registers _except_ `$ggas` and `$cgas`. Afterwards, set the following registers:
1. `$pc = $pc + 4` (advance program counter from where we called)

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

Panic if:
- `$sp + $rs` overflows
- `$sp + $rs > $hp`

### CFS: Shrink call frame

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Shrink the current call frame's stack. |
| Operation   | ```$sp = $sp - $rs```                  |
| Syntax      | `cfs $rs`                              |
| Encoding    | `0x00 rs - - -`                        |
| Notes       | Does not clear memory.                 |

Panic if:
- `$sp - $rs` underflows
- `$sp - $rs < $ssp`

### LB: Load byte

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | A byte is loaded from the specified address offset by `imm`. |
| Operation   | ```$rd = MEM[$rs + imm, 1];```                               |
| Syntax      | `lb $rd, $rs, imm`                                           |
| Encoding    | `0x00 rd rs i i`                                             |
| Notes       |                                                              |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
- `$rs + imm + 1` overflows
- `$rs + imm + 1 > VM_MAX_RAM`

### LW: Load word

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | A word is loaded from the specified address offset by `imm`. |
| Operation   | ```$rd = MEM[$rs + imm, 8];```                               |
| Syntax      | `lw $rd, $rs, imm`                                           |
| Encoding    | `0x00 rd rs i i`                                             |
| Notes       |                                                              |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
- `$rs + imm + 8` overflows
- `$rs + imm + 8 > VM_MAX_RAM`

### MALLOC: Allocate memory

|             |                                           |
| ----------- | ----------------------------------------- |
| Description | Allocate a number of bytes from the heap. |
| Operation   | ```$hp = $hp - $rs;```                    |
| Syntax      | `malloc $rs`                              |
| Encoding    | `0x00 rs - - -`                           |
| Notes       | Does not initialize memory.               |

Panic if:
- `$hp - $rs` underflows
- `$hp - $rs < $sp`

### MEMCLEAR: Memory clear

|             |                          |
| ----------- | ------------------------ |
| Description | Clear bytes in memory.   |
| Operation   | ```MEM[$rd, $rs] = 0;``` |
| Syntax      | `memclear $rs`           |
| Encoding    | `0x00 rd rs - -`         |
| Notes       |                          |

Panic if:
* `$rd + $rs` overflows
* `$rd + $rs > VM_MAX_RAM`
* `$rs > MEM_MAX_ACCESS_SIZE`
* The memory range `MEM[$rd, $rs]`  does not pass [ownership check](./main.md#ownership)

### MEMCP: Memory copy

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rd, $rt] = MEM[$rs, $rt];``` |
| Syntax      | `memcp $rd, $rs, $rt`                |
| Encoding    | `0x00 rd rs rt -`                    |
| Notes       |                                      |

Panic if:
* `$rd + $rt` overflows
* `$rs + $rt` overflows
* `$rd + $rt > VM_MAX_RAM`
* `$rs + $rt > VM_MAX_RAM`
* `$rt > MEM_MAX_ACCESS_SIZE`
* The memory range `MEM[$rd, $rt]`  does not pass [ownership check](./main.md#ownership)

### MEMEQ: Memory equality

|             |                                             |
| ----------- | ------------------------------------------- |
| Description | Compare bytes in memory.                    |
| Operation   | ```$rd = MEM[$rs, $ru] == MEM[$rt, $ru];``` |
| Syntax      | `memeq $rd, $rs, $rt, $ru`                  |
| Encoding    | `0x00 rd rs rt ru`                          |
| Notes       |                                             |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
* `$rs + $ru` overflows
* `$rt + $ru` overflows
* `$rs + $ru > VM_MAX_RAM`
* `$rt + $ru > VM_MAX_RAM`
* `$ru > MEM_MAX_ACCESS_SIZE`

### SB: Store byte

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | The least significant byte of `$rs` is stored at the address `$rd` offset by `imm`. |
| Operation   | ```MEM[$rd + imm, 1] = $rs[7, 1];```                                                |
| Syntax      | `sb $rd, $rs, imm`                                                                  |
| Encoding    | `0x00 rd rs i i`                                                                    |
| Notes       |                                                                                     |

Panic if:
* `$rd + imm + 1` overflows
* `$rd + imm + 1 > VM_MAX_RAM`
* The memory range `MEM[$rd + imm, 1]`  does not pass [ownership check](./main.md#ownership)

### SW: Store word

|             |                                                                    |
| ----------- | ------------------------------------------------------------------ |
| Description | The value of `$rs` is stored at the address `$rd` offset by `imm`. |
| Operation   | ```MEM[$rd + imm, 8] = $rs;```                                     |
| Syntax      | `sw $rd, $rs, imm`                                                 |
| Encoding    | `0x00 rd rs i i`                                                   |
| Notes       |                                                                    |

Panic if:
* `$rd + imm + 8` overflows
* `$rd + imm + 8 > VM_MAX_RAM`
* The memory range `MEM[$rd + imm, 8]`  does not pass [ownership check](./main.md#ownership)

## Contract Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation, except for [CALL](#call-call-contract) and [REVERT](#revert-revert).

### BLOCKHASH: Block hash

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Get block header hash.               |
| Operation   | ```MEM[$rd, 32] = blockhash($rs);``` |
| Syntax      | `blockhash $rd $rs`                  |
| Encoding    | `0x00 rd rs - -`                     |
| Notes       |                                      |

Panic if:
* `$rd + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* The memory range `MEM[$rd, 32]`  does not pass [ownership check](./main.md#ownership)

Block header hashes for blocks with height greater than or equal to current block height are zero (`0x00**32`).

### BLOCKHEIGHT: Block height

|             |                            |
| ----------- | -------------------------- |
| Description | Get Fuel block height.     |
| Operation   | ```$rd = blockheight();``` |
| Syntax      | `blockheight $rd`          |
| Encoding    | `0x00 rd - - -`            |
| Notes       |                            |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)

### CALL: Call contract

|             |                    |
| ----------- | ------------------ |
| Description | Call contract.     |
| Operation   |                    |
| Syntax      | `call $rs $rt $ru` |
| Encoding    | `0x00 rs rt ru -`  |
| Notes       |                    |

Panic if:
* Contract with ID `MEM[$rs, 32]` is not in `tx.inputs`
* Reading past `MEM[VM_MAX_RAM - 1]`
* Any output range does not pass [ownership check](./main.md#ownership)
* In an external context, if `$rt > $bal`
* In an internal context, if `$rt` is greater than `amount` of output with contract ID `MEM[$rs, 32]`

Register `$rs` is a memory address from which the following fields are set (word-aligned):

| bytes | type                 | value             | description                                                      |
| ----- | -------------------- | ----------------- | ---------------------------------------------------------------- |
| 32    | `byte[32]`           | to                | Contract ID to call.                                             |
| 8     | `uint8`              | out count         | Number of return values.                                         |
| 8     | `uint8`              | in count          | Number of input values.                                          |
| 16*   | `(uint32, uint32)[]` | out (addr, size)s | Array of memory addresses and lengths in bytes of return values. |
| 16*   | `(uint32, uint32)[]` | in (addr, size)s  | Array of memory addresses and lengths in bytes of input values.  |

`$ru` is the amount of gas to forward. If it is set to an amount greater than the available gas, all available gas is forwarded.

For output with contract ID `MEM[$rs, 32]`, increase `amount` by `$rt`. In an external context, decrease `$bal` by `$rt`. In an internal context, decrease `amount` of output with contract ID `MEM[$fp, 32]`.

A [call frame](./main.md#call-frames) is pushed at `$sp`. In addition to filling in the values of the call frame, the following registers are set:
1. `$fp = $sp` (on top of the previous call frame is the beginning of this call frame)
1. Set `$ssp` and `$sp` to the start of the writable stack area of the call frame.
1. Set `$pc` and `$is` to the starting address of the code.
1. `$bal = $rt` (forward coins)
1. `$cgas = $ru` (forward gas)

### CODECOPY: Code copy

|             |                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Description | Copy `$ru` bytes of code starting at `$rt` for contract with ID equal to the 32 bytes in memory starting at `$rs` into memory starting at `$rd`. |
| Operation   | ```MEM[$rd, $ru] = code($rs, $rt, $ru);```                                                                                                       |
| Syntax      | `codecopy $rd, $rs, $rt, $ru`                                                                                                                    |
| Encoding    | `0x00 rd rs rt ru`                                                                                                                               |
| Notes       | If `$ru` is greater than the code size, zero bytes are filled in.                                                                                |

Panic if:
* `$rd + $ru` overflows
* `$rs + 32` overflows
* `$rd + $ru > VM_MAX_RAM`
* `$rs + 32 > VM_MAX_RAM`
* The memory range `MEM[$rd, $ru]`  does not pass [ownership check](./main.md#ownership)
* `$ru > MEM_MAX_ACCESS_SIZE`
* Contract with ID `MEM[$rs, 32]` is not in `tx.inputs`

### CODEROOT: Code Merkle root

|             |                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Set the 32 bytes in memory starting at `$rd` to the code root for contract with ID equal to the 32 bytes in memory starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = coderoot(MEM[$rs, 32]);```                                                                                          |
| Syntax      | `codehash $rd, $rs`                                                                                                                   |
| Encoding    | `0x00 rd rs - -`                                                                                                                      |
| Notes       |                                                                                                                                       |

Panic if:
* `$rd + 32` overflows
* `$rs + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs + 32 > VM_MAX_RAM`
* The memory range `MEM[$rd, 32]`  does not pass [ownership check](./main.md#ownership)
* Contract with ID `MEM[$rs, 32]` is not in `tx.inputs`

### CODESIZE: Code size

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Set `$rd` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rs`. |
| Operation   | ```$rd = codesize(MEM[$rs, 32]);```                                                                       |
| Syntax      | `codesize $rd, $rs`                                                                                       |
| Encoding    | `0x00 rd rs - -`                                                                                          |
| Notes       |                                                                                                           |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
* `$rs + 32` overflows
* `$rs + 32 > VM_MAX_RAM`
* Contract with ID `MEM[$rs, 32]` is not in `tx.inputs`

### COINBASE: Block proposer address

|             |                                  |
| ----------- | -------------------------------- |
| Description | Get block proposer address.      |
| Operation   | ```MEM[$rd, 32] = coinbase();``` |
| Syntax      | `coinbase $rd`                   |
| Encoding    | `0x00 rd - - -`                  |
| Notes       |                                  |

Panic if:
* `$rd + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* The memory range `MEM[$rd, 32]`  does not pass [ownership check](./main.md#ownership)

### LOADCODE: Load code from an external contract

|             |                                                                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Copy `$ru` bytes of code starting at `$rt` for contract with ID equal to the 32 bytes in memory starting at `$rs` into memory starting at `$ssp`. |
| Operation   | ```MEM[$ssp, $ru] = code($rs, $rt, $ru);```                                                                                                       |
| Syntax      | `loadcode $rs, $rt, $ru`                                                                                                                          |
| Encoding    | `0x00 rs rt ru -`                                                                                                                                 |
| Notes       | If `$ru` is greater than the code size, zero bytes are filled in.                                                                                 |

Panic if:
* `$ssp + $ru` overflows
* `$rs + 32` overflows
* `$ssp + $ru > VM_MAX_RAM`
* `$rs + 32 > VM_MAX_RAM`
* `$ssp != $sp`
* `$ssp + $ru > $hp`
* `$ru > CONTRACT_MAX_SIZE`
* `$ru > MEM_MAX_ACCESS_SIZE`
* Contract with ID `MEM[$rs, 32]` is not in `tx.inputs`

Increment `$hp->codesize`, `$ssp`, and `$sp` by `$ru` padded to word alignment.

This opcode can be used to concatenate the code of multiple contracts together. It can only be used when the stack area of the call frame is unused (i.e. prior to being used).

### LOG: Log event

|             |                                |
| ----------- | ------------------------------ |
| Description | Log an event. This is a no-op. |
| Operation   | ```log($rs, $rt, $ru, $rv);``` |
| Syntax      | `log $rs, $rt, $ru, $rv`       |
| Encoding    | `0x00 rs rt ru rv`             |
| Notes       |                                |

### REVERT: Revert

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Halt execution, reverting state changes and returning value in `$rs`. |
| Operation   | ```revert($rs);```                                                    |
| Syntax      | `revert $rs`                                                          |
| Encoding    | `0x00 rs - - -`                                                       |
| Notes       |                                                                       |

After a revert:
1. All [OutputContract](../protocol/tx_format.md#outputcontract) outputs will have the same `amount` and `stateRoot` as on initialization.
1. All [OutputVariable](../protocol/tx_format.md outputs#outputvariable) outputs will have `to` and `amount` of zero.
1. All [OutputContractConditional](../protocol/tx_format.md#outputcontractconditional) outputs will have `contractID`, `amount`, and `stateRoot` of zero.

### SRW: State read word

|             |                                                   |
| ----------- | ------------------------------------------------- |
| Description | A word is read from the current contract's state. |
| Operation   | ```$rd = STATE[MEM[$rs, 32]][0, 8];```            |
| Syntax      | `srw $rd, $rs`                                    |
| Encoding    | `0x00 rd rs - -`                                  |
| Notes       | Returns zero if the state element does not exist. |

Panic if:
* `$rd` is a [reserved register](./main.md#semantics)
* `$rs + 32` overflows
* `$rs + 32 > VM_MAX_RAM`
* `$fp == 0` (in the script context)

### SRWX: State read 32 bytes

|             |                                                     |
| ----------- | --------------------------------------------------- |
| Description | 32 bytes is read from the current contract's state. |
| Operation   | ```MEM[$rd, 32] = STATE[MEM[$rs, 32]];```           |
| Syntax      | `srwx $rd, $rs`                                     |
| Encoding    | `0x00 rd rs - -`                                    |
| Notes       | Returns zero if the state element does not exist.   |

Panic if:
* `$rd + 32` overflows
* `$rs + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs + 32 > VM_MAX_RAM`
* The memory range `MEM[$rd, 32]`  does not pass [ownership check](./main.md#ownership)
* `$fp == 0` (in the script context)

### SWW: State write word

|             |                                                    |
| ----------- | -------------------------------------------------- |
| Description | A word is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rd, 32]][0, 8] = $rs;```             |
| Syntax      | `sww $rd $rs`                                      |
| Encoding    | `0x00 rd rs - -`                                   |
| Notes       |                                                    |

Panic if:
* `$rd + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$fp == 0` (in the script context)

### SWWX: State write 32 bytes

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | 32 bytes is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rd, 32]] = MEM[$rs, 32];```            |
| Syntax      | `swwx $rd, $rs`                                      |
| Encoding    | `0x00 rd rs - -`                                     |
| Notes       |                                                      |

Panic if:
* `$rd + 32` overflows
* `$rs + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs + 32 > VM_MAX_RAM`
* `$fp == 0` (in the script context)

### TRANSFER: Transfer coins

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Transfer `$rt` coins to address at `$rd`, with output `$rs`. |
| Operation   | ```transfer(MEM[$rd, 32], $rs, $rt);```                      |
| Syntax      | `transfer $rd, $rs, rt`                                      |
| Encoding    | `0x00 rd rs rt -`                                            |
| Notes       |                                                              |

Panic if:
* `$rd + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs > tx.outputsCount`
* In an external context, if `$rt > $bal`
* In an internal context, if `$rt` is greater than `amount` of output with contract ID `MEM[$fp, 32]`
* `$rt == 0`
* `tx.outputs[$rs].type != OutputType.Variable`
* `tx.outputs[$rs].amount != 0`

In an external context, decrease `$bal` by `$rt`. In an internal context, decrease `amount` of output with contract ID `MEM[$fp, 32]`. Then set:
* `tx.outputs[$rs].to = MEM[$rd, 32]`
* `tx.outputs[$rs].amount = $rt`

## Cryptographic Opcodes

### ECRECOVER: Signature recovery

|             |                                                                                                                             |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- |
| Description | The 64-byte public key (x, y) recovered from 64-byte signature starting at `$rs` on 32-byte message hash starting at `$rt`. |
| Operation   | ```MEM[$rd, 64] = ecrecover(MEM[$rs, 64], MEM[$rt, 32]);```                                                                 |
| Syntax      | `ecrecover $rd, $rs, $rt`                                                                                                   |
| Encoding    | `0x00 rd rs rt -`                                                                                                           |
| Notes       |                                                                                                                             |

Panic if:
* `$rd + 64` overflows
* `$rs + 64` overflows
* `$rt + 32` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs + 64 > VM_MAX_RAM`
* `$rt + 32 > VM_MAX_RAM`
* The memory range `MEM[$rd, 64]`  does not pass [ownership check](./main.md#ownership)

To get the address, hash the public key with [SHA-2-256](#sha256-sha-2-256).

### KECCAK256: keccak-256

|             |                                                       |
| ----------- | ----------------------------------------------------- |
| Description | The keccak-256 hash of `$rt` bytes starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = keccak256(MEM[$rs, $rt]);```        |
| Syntax      | `keccak256 $rd, $rs, $rt`                             |
| Encoding    | `0x00 rd rs rt -`                                     |
| Notes       |                                                       |

Panic if:
* `$rd + 64` overflows
* `$rs + $rt` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs + $rt > VM_MAX_RAM`
* The memory range `MEM[$rd, 32]`  does not pass [ownership check](./main.md#ownership)
* `$rt > MEM_MAX_ACCESS_SIZE`

### SHA256: SHA-2-256

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | The SHA-2-256 hash of `$rt` bytes starting at `$rs`. |
| Operation   | ```MEM[$rd, 32] = sha256(MEM[$rs, $rt]);```          |
| Syntax      | `sha256 $rd, $rs, $rt`                               |
| Encoding    | `0x00 rd rs rt -`                                    |
| Notes       |                                                      |

Panic if:
* `$rd + 64` overflows
* `$rs + $rt` overflows
* `$rd + 32 > VM_MAX_RAM`
* `$rs + $rt > VM_MAX_RAM`
* The memory range `MEM[$rd, 32]`  does not pass [ownership check](./main.md#ownership)
* `$rt > MEM_MAX_ACCESS_SIZE`

## Other Opcodes

### FLAG: Set flags

|             |                       |
| ----------- | --------------------- |
| Description | Set `$flag` to `$rs`. |
| Operation   | ```$flag = $rs;```    |
| Syntax      | `flag $rs`            |
| Encoding    | `0x00 rs - - -`       |
| Notes       |                       |
