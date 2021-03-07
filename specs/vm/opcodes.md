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
  - [SLOADCODE: Load code from static list](#sloadcode-load-code-from-static-list)
  - [SRW: State read word](#srw-state-read-word)
  - [SRWX: State read 32 bytes](#srwx-state-read-32-bytes)
  - [SWW: State write word](#sww-state-write-word)
  - [SWWX: State write 32 bytes](#swwx-state-write-32-bytes)
  - [TRANSFER: Transfer coins to contract](#transfer-transfer-coins-to-contract)
  - [TRANSFEROUT: Transfer coins to output](#transferout-transfer-coins-to-output)
- [Cryptographic Opcodes](#cryptographic-opcodes)
  - [ECRECOVER: Signature recovery](#ecrecover-signature-recovery)
  - [KECCAK256: keccak-256](#keccak256-keccak-256)
  - [SHA256: SHA-2-256](#sha256-sha-2-256)
- [Other Opcodes](#other-opcodes)
  - [FLAG: Set flags](#flag-set-flags)

## Reading Guide

This page provides a description of all opcodes for the FuelVM. Encoding is read as a sequence of one 8-bit value (the opcode identifier) followed by four 6-bit values (the register identifiers or immediate value). A single `i` indicates a 6-bit immediate value, `i i` indicates a 12-bit immediate value, `i i i` indicates an 18-bit immediate value, and `i i i i` indicates a 24-bit immediate value. All immediate values are interpreted as big-endian unsigned integers.

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
| Operation   | ```$rA = $rB + $rC;``` |
| Syntax      | `add $rA, $rB, $rC`    |
| Encoding    | `0x00 rA rB rC -`      |
| Notes       |                        |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### ADDI: Add immediate

|             |                                         |
| ----------- | --------------------------------------- |
| Description | Adds a register and an immediate value. |
| Operation   | ```$rA = $rB + imm;```                  |
| Syntax      | `addi $rA, $rB, immediate`              |
| Encoding    | `0x00 rA rB i i`                        |
| Notes       |                                         |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### AND: AND

|             |                             |
| ----------- | --------------------------- |
| Description | Bitwise ANDs two registers. |
| Operation   | ```$rA = $rB & $rC;```      |
| Syntax      | `and $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`           |
| Notes       |                             |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### ANDI: AND immediate

|             |                                                 |
| ----------- | ----------------------------------------------- |
| Description | Bitwise ANDs a register and an immediate value. |
| Operation   | ```$rA = $rB & imm;```                          |
| Syntax      | `andi $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                                |
| Notes       |                                                 |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### DIV: Divide

|             |                         |
| ----------- | ----------------------- |
| Description | Divides two registers.  |
| Operation   | ```$rA = $rB // $rC;``` |
| Syntax      | `div $rA, $rB, $rC`     |
| Encoding    | `0x00 rA rB rC -`       |
| Notes       |                         |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If `$rC == 0`, `$rA` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### DIVI: Divide immediate

|             |                                            |
| ----------- | ------------------------------------------ |
| Description | Divides a register and an immediate value. |
| Operation   | ```$rA = $rB // imm;```                    |
| Syntax      | `divi $rA, $rB, imm`                       |
| Encoding    | `0x00 rA rB i i`                           |
| Notes       |                                            |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If `imm == 0`, `$rA` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### EQ: Equals

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Compares two registers for equality. |
| Operation   | ```$rA = $rB == $rC;```              |
| Syntax      | `eq $rA, $rB, $rC`                   |
| Encoding    | `0x00 rA rB rC -`                    |
| Notes       |                                      |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### EXP: Exponentiate

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | Raises one register to the power of another. |
| Operation   | ```$rA = $rB ** $rC;```                      |
| Syntax      | `exp $rA, $rB, $rC`                          |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If the result cannot fit in 8 bytes, `$of` is set to `1`, otherwise `$of` is cleared.

`$err` is cleared.

### EXPI: Exponentiate immediate

|             |                                                         |
| ----------- | ------------------------------------------------------- |
| Description | Raises one register to the power of an immediate value. |
| Operation   | ```$rA = $rB ** imm;```                                 |
| Syntax      | `expi $rA, $rB, imm`                                    |
| Encoding    | `0x00 rA rB i i`                                        |
| Notes       |                                                         |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If the result cannot fit in 8 bytes, `$of` is set to `1`, otherwise `$of` is cleared.

`$err` is cleared.

### GT: Greater than

|             |                                          |
| ----------- | ---------------------------------------- |
| Description | Compares two registers for greater-than. |
| Operation   | ```$rA = $rB > $rC;```                   |
| Syntax      | `gt $rA, $rB, $rC`                       |
| Encoding    | `0x00 rA rB rC -`                        |
| Notes       |                                          |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### MATHLOG: Math logarithm

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | The (integer) logarithm base `$rC` of `$rB`. |
| Operation   | ```$rA = math.floor(math.log($rB, $rC));```  |
| Syntax      | `mathlog $rA, $rB, $rC`                      |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If `$rB == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

If `$rC <= 1`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MATHROOT: Math root

|             |                                              |
| ----------- | -------------------------------------------- |
| Description | The (integer) `$rC`th root of `$rB`.         |
| Operation   | ```$rA = math.floor(math.root($rB, $rC));``` |
| Syntax      | `mathroot $rA, $rB, $rC`                     |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If `$rC == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MOD: Modulus

|             |                                    |
| ----------- | ---------------------------------- |
| Description | Modulo remainder of two registers. |
| Operation   | ```$rA = $rB % $rC;```             |
| Syntax      | `mod $rA, $rB, $rC`                |
| Encoding    | `0x00 rA rB rC -`                  |
| Notes       |                                    |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If `$rC == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MODI: Modulus immediate

|             |                                                        |
| ----------- | ------------------------------------------------------ |
| Description | Modulo remainder of a register and an immediate value. |
| Operation   | ```$rA = $rB % imm;```                                 |
| Syntax      | `modi $rA, $rB, imm`                                   |
| Encoding    | `0x00 rA rB i i`                                       |
| Notes       |                                                        |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

If `imm == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MUL: Multiply

|             |                           |
| ----------- | ------------------------- |
| Description | Multiplies two registers. |
| Operation   | ```$rA = $rB * $rC;```    |
| Syntax      | `mul $rA, $rB, $rC`       |
| Encoding    | `0x00 rA rB rC -`         |
| Notes       |                           |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### MULI: Multiply immediate

|             |                                               |
| ----------- | --------------------------------------------- |
| Description | Multiplies a register and an immediate value. |
| Operation   | ```$rA = $rB * imm;```                        |
| Syntax      | `mul $rA, $rB, imm`                           |
| Encoding    | `0x00 rA rB i i`                              |
| Notes       |                                               |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

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
| Operation   | ```$rA = ~$rB;```       |
| Syntax      | `not $rA, $rB`          |
| Encoding    | `0x00 rA rB - -`        |
| Notes       |                         |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### OR: OR

|             |                            |
| ----------- | -------------------------- |
| Description | Bitwise ORs two registers. |
| Operation   | ```$rA = $rB | $rC;```     |
| Syntax      | `or $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`          |
| Notes       |                            |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### ORI: OR immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Bitwise ORs a register and an immediate value. |
| Operation   | ```$rA = $rB | imm;```                         |
| Syntax      | `ori $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                               |
| Notes       |                                                |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### SLL: Shift left logical

|             |                                       |
| ----------- | ------------------------------------- |
| Description | Left shifts a register by a register. |
| Operation   | ```$rA = $rB << $rC;```               |
| Syntax      | `sll $rA, $rB, $rC`                   |
| Encoding    | `0x00 rA rB rC -`                     |
| Notes       | Zeroes are shifted in.                |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### SLLI: Shift left logical immediate

|             |                                               |
| ----------- | --------------------------------------------- |
| Description | Left shifts a register by an immediate value. |
| Operation   | ```$rA = $rB << imm;```                       |
| Syntax      | `slli $rA, $rB, imm`                          |
| Encoding    | `0x00 rA rB i i`                              |
| Notes       | Zeroes are shifted in.                        |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### SRL: Shift right logical

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Right shifts a register by a register. |
| Operation   | ```$rA = $rB >> $rC;```                |
| Syntax      | `srl $rA, $rB, $rC`                    |
| Encoding    | `0x00 rA rB rC -`                      |
| Notes       | Zeroes are shifted in.                 |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SRLI: Shift right logical immediate

|             |                                                |
| ----------- | ---------------------------------------------- |
| Description | Right shifts a register by an immediate value. |
| Operation   | ```$rA = $rB >> imm;```                        |
| Syntax      | `srli $rA, $rB, imm`                           |
| Encoding    | `0x00 rA rB i i`                               |
| Notes       | Zeroes are shifted in.                         |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SUB: Subtract

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Subtracts two registers.                         |
| Operation   | ```$rA = $rB - $rC;```                           |
| Syntax      | `sub $rA, $rB, $rC`                              |
| Encoding    | `0x00 rA rB rC -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SUBI: Subtract immediate

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Subtracts a register and an immediate value.     |
| Operation   | ```$rA = $rB - $rC;```                           |
| Syntax      | `subi $rA, $rB, $rC`                             |
| Encoding    | `0x00 rA rB rC -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### XOR: XOR

|             |                             |
| ----------- | --------------------------- |
| Description | Bitwise XORs two registers. |
| Operation   | ```$rA = $rB ^ $rC;```      |
| Syntax      | `xor $rA, $rB, $rC `        |
| Encoding    | `0x00 rA rB rC -`           |
| Notes       |                             |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### XORI: XOR immediate

|             |                                                 |
| ----------- | ----------------------------------------------- |
| Description | Bitwise XORs a register and an immediate value. |
| Operation   | ```$rA = $rB ^ imm;```                          |
| Syntax      | `xori $rA, $rB, imm `                           |
| Encoding    | `0x00 rA rB i i`                                |
| Notes       |                                                 |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

## Control Flow Opcodes

### CIMV: Check input maturity verify

|             |                                                             |
| ----------- | ----------------------------------------------------------- |
| Description | Set `$rA` to `true` if the `$rC <= tx.input[$rB].maturity`. |
| Operation   | ```$rA = checkinputmaturityverify($rB, $rC);```             |
| Syntax      | `cimv $rA $rB $rC`                                          |
| Encoding    | `0x00 rA rB rC -`                                           |
| Notes       |                                                             |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
* `$rC > tx.input[$rs].maturity`
* the input `$rB` is not of type [`InputType.Coin`](../protocol/tx_format.md)
* `$rB > tx.inputsCount`

Otherwise, advance the program counter `$pc` by `4`.

See also: [BIP-112](https://github.com/bitcoin/bips/blob/master/bip-0112.mediawiki) and [CLTV](#cltv-check-lock-time-verify).

### CTMV: Check transaction maturity verify

|             |                                                  |
| ----------- | ------------------------------------------------ |
| Description | Set `$rA` to `true` if `$rB <= tx.maturity`.     |
| Operation   | ```$rA = checktransactionmaturityverify($rB);``` |
| Syntax      | `ctmv $rA $rB`                                   |
| Encoding    | `0x00 rA rB - -`                                 |
| Notes       |                                                  |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
* `$rB > tx.maturity`

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
| Description | Jump to the code instruction offset by `imm` if `$rA` is not zero.                     |
| Operation   | ```if $rA != 0:```<br>```  $pc = $is + imm * 4;```<br>```else:```<br>```  $pc += 4;``` |
| Syntax      | `jnzi $rA`                                                                             |
| Encoding    | `0x00 rA i i i`                                                                        |
| Notes       |                                                                                        |

Panic if:
* `$is + imm * 4 > VM_MAX_RAM - 1`

### RETURN: Return from context

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | Returns from [context](./main.md#contexts) with value `$rA`. |
| Operation   | ```return($rA);```                                           |
| Syntax      | `return $rA`                                                 |
| Encoding    | `0x00 rA - - -`                                              |
| Notes       |                                                              |

If current context is external, cease VM execution and return `$rA`.

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
| Operation   | ```$sp = $sp + $rA```                  |
| Syntax      | `cfe $rA`                              |
| Encoding    | `0x00 rA - - -`                        |
| Notes       | Does not initialize memory.            |

Panic if:
- `$sp + $rA` overflows
- `$sp + $rA > $hp`

### CFS: Shrink call frame

|             |                                        |
| ----------- | -------------------------------------- |
| Description | Shrink the current call frame's stack. |
| Operation   | ```$sp = $sp - $rA```                  |
| Syntax      | `cfs $rA`                              |
| Encoding    | `0x00 rA - - -`                        |
| Notes       | Does not clear memory.                 |

Panic if:
- `$sp - $rA` underflows
- `$sp - $rA < $ssp`

### LB: Load byte

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | A byte is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + imm, 1];```                               |
| Syntax      | `lb $rA, $rB, imm`                                           |
| Encoding    | `0x00 rA rB i i`                                             |
| Notes       |                                                              |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
- `$rB + imm + 1` overflows
- `$rB + imm + 1 > VM_MAX_RAM`

### LW: Load word

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| Description | A word is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + imm, 8];```                               |
| Syntax      | `lw $rA, $rB, imm`                                           |
| Encoding    | `0x00 rA rB i i`                                             |
| Notes       |                                                              |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
- `$rB + imm + 8` overflows
- `$rB + imm + 8 > VM_MAX_RAM`

### MALLOC: Allocate memory

|             |                                           |
| ----------- | ----------------------------------------- |
| Description | Allocate a number of bytes from the heap. |
| Operation   | ```$hp = $hp - $rA;```                    |
| Syntax      | `malloc $rA`                              |
| Encoding    | `0x00 rA - - -`                           |
| Notes       | Does not initialize memory.               |

Panic if:
- `$hp - $rA` underflows
- `$hp - $rA < $sp`

### MEMCLEAR: Memory clear

|             |                          |
| ----------- | ------------------------ |
| Description | Clear bytes in memory.   |
| Operation   | ```MEM[$rA, $rB] = 0;``` |
| Syntax      | `memclear $rA, $rB`      |
| Encoding    | `0x00 rA rB - -`         |
| Notes       |                          |

Panic if:
* `$rA + $rB` overflows
* `$rA + $rB > VM_MAX_RAM`
* `$rB > MEM_MAX_ACCESS_SIZE`
* The memory range `MEM[$rA, $rB]`  does not pass [ownership check](./main.md#ownership)

### MEMCP: Memory copy

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rA, $rC] = MEM[$rB, $rC];``` |
| Syntax      | `memcp $rA, $rB, $rC`                |
| Encoding    | `0x00 rA rB rC -`                    |
| Notes       |                                      |

Panic if:
* `$rA + $rC` overflows
* `$rB + $rC` overflows
* `$rA + $rC > VM_MAX_RAM`
* `$rB + $rC > VM_MAX_RAM`
* `$rC > MEM_MAX_ACCESS_SIZE`
* The memory range `MEM[$rA, $rC]`  does not pass [ownership check](./main.md#ownership)

### MEMEQ: Memory equality

|             |                                             |
| ----------- | ------------------------------------------- |
| Description | Compare bytes in memory.                    |
| Operation   | ```$rA = MEM[$rB, $rD] == MEM[$rC, $rD];``` |
| Syntax      | `memeq $rA, $rB, $rC, $rD`                  |
| Encoding    | `0x00 rA rB rC rD`                          |
| Notes       |                                             |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
* `$rB + $rD` overflows
* `$rC + $rD` overflows
* `$rB + $rD > VM_MAX_RAM`
* `$rC + $rD > VM_MAX_RAM`
* `$rD > MEM_MAX_ACCESS_SIZE`

### SB: Store byte

|             |                                                                                     |
| ----------- | ----------------------------------------------------------------------------------- |
| Description | The least significant byte of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + imm, 1] = $rB[7, 1];```                                                |
| Syntax      | `sb $rA, $rB, imm`                                                                  |
| Encoding    | `0x00 rA rB i i`                                                                    |
| Notes       |                                                                                     |

Panic if:
* `$rA + imm + 1` overflows
* `$rA + imm + 1 > VM_MAX_RAM`
* The memory range `MEM[$rA + imm, 1]`  does not pass [ownership check](./main.md#ownership)

### SW: Store word

|             |                                                                    |
| ----------- | ------------------------------------------------------------------ |
| Description | The value of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + imm, 8] = $rB;```                                     |
| Syntax      | `sw $rA, $rB, imm`                                                 |
| Encoding    | `0x00 rA rB i i`                                                   |
| Notes       |                                                                    |

Panic if:
* `$rA + imm + 8` overflows
* `$rA + imm + 8 > VM_MAX_RAM`
* The memory range `MEM[$rA + imm, 8]`  does not pass [ownership check](./main.md#ownership)

## Contract Opcodes

All these opcodes advance the program counter `$pc` by `4` after performing their operation, except for [CALL](#call-call-contract) and [REVERT](#revert-revert).

### BLOCKHASH: Block hash

|             |                                      |
| ----------- | ------------------------------------ |
| Description | Get block header hash.               |
| Operation   | ```MEM[$rA, 32] = blockhash($rB);``` |
| Syntax      | `blockhash $rA $rB`                  |
| Encoding    | `0x00 rA rB - -`                     |
| Notes       |                                      |

Panic if:
* `$rA + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)

Block header hashes for blocks with height greater than or equal to current block height are zero (`0x00**32`).

### BLOCKHEIGHT: Block height

|             |                            |
| ----------- | -------------------------- |
| Description | Get Fuel block height.     |
| Operation   | ```$rA = blockheight();``` |
| Syntax      | `blockheight $rA`          |
| Encoding    | `0x00 rA - - -`            |
| Notes       |                            |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)

### CALL: Call contract

|             |                        |
| ----------- | ---------------------- |
| Description | Call contract.         |
| Operation   |                        |
| Syntax      | `call $rA $rB $rC $rD` |
| Encoding    | `0x00 rA rB rC rD`     |
| Notes       |                        |

Panic if:
* `$rA + 32` overflows
* `$rC + 32` overflows
* Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
* Reading past `MEM[VM_MAX_RAM - 1]`
* Any output range does not pass [ownership check](./main.md#ownership)
* In an external context, if `$rB > MEM[balanceOfStart(MEM[$rC, 32]), 8]`
* In an internal context, if `$rB` is greater than the balance of color `MEM[$rC, 32]` of output with contract ID `MEM[$rA, 32]`

Register `$rA` is a memory address from which the following fields are set (word-aligned):

| bytes | type                 | value             | description                                                      |
| ----- | -------------------- | ----------------- | ---------------------------------------------------------------- |
| 32    | `byte[32]`           | to                | Contract ID to call.                                             |
| 8     | `uint8`              | out count         | Number of return values.                                         |
| 8     | `uint8`              | in count          | Number of input values.                                          |
| 16*   | `(uint32, uint32)[]` | out (addr, size)s | Array of memory addresses and lengths in bytes of return values. |
| 16*   | `(uint32, uint32)[]` | in (addr, size)s  | Array of memory addresses and lengths in bytes of input values.  |

`$rD` is the amount of gas to forward. If it is set to an amount greater than the available gas, all available gas is forwarded.

For output with contract ID `MEM[$rA, 32]`, increase balance of color `MEM[$rC, 32]` by `$rB`. In an external context, decrease `MEM[balanceOfStart(MEM[$rC, 32]), 8]` by `$rB`. In an internal context, decrease color `MEM[$rC, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rB`.

A [call frame](./main.md#call-frames) is pushed at `$sp`. In addition to filling in the values of the call frame, the following registers are set:
1. `$fp = $sp` (on top of the previous call frame is the beginning of this call frame)
1. Set `$ssp` and `$sp` to the start of the writable stack area of the call frame.
1. Set `$pc` and `$is` to the starting address of the code.
1. `$bal = $rD` (forward coins)
1. `$cgas = $rD` or all available gas (forward gas)

This modifies the `balanceRoot` field of the appropriate output(s).

### CODECOPY: Code copy

|             |                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Description | Copy `$rD` bytes of code starting at `$rC` for contract with ID equal to the 32 bytes in memory starting at `$rB` into memory starting at `$rA`. |
| Operation   | ```MEM[$rA, $rD] = code($rB, $rC, $rD);```                                                                                                       |
| Syntax      | `codecopy $rA, $rB, $rC, $rD`                                                                                                                    |
| Encoding    | `0x00 rA rB rC rD`                                                                                                                               |
| Notes       | If `$rD` is greater than the code size, zero bytes are filled in.                                                                                |

Panic if:
* `$rA + $rD` overflows
* `$rB + 32` overflows
* `$rA + $rD > VM_MAX_RAM`
* `$rB + 32 > VM_MAX_RAM`
* The memory range `MEM[$rA, $rD]`  does not pass [ownership check](./main.md#ownership)
* `$rD > MEM_MAX_ACCESS_SIZE`
* Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

### CODEROOT: Code Merkle root

|             |                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Set the 32 bytes in memory starting at `$rA` to the code root for contract with ID equal to the 32 bytes in memory starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = coderoot(MEM[$rB, 32]);```                                                                                          |
| Syntax      | `coderoot $rA, $rB`                                                                                                                   |
| Encoding    | `0x00 rA rB - -`                                                                                                                      |
| Notes       |                                                                                                                                       |

Panic if:
* `$rA + 32` overflows
* `$rB + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rB + 32 > VM_MAX_RAM`
* The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
* Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

Code root compuration is defined [here](../protocol/identifiers.md#contract-id).

### CODESIZE: Code size

|             |                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| Description | Set `$rA` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rB`. |
| Operation   | ```$rA = codesize(MEM[$rB, 32]);```                                                                       |
| Syntax      | `codesize $rA, $rB`                                                                                       |
| Encoding    | `0x00 rA rB - -`                                                                                          |
| Notes       |                                                                                                           |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
* `$rB + 32` overflows
* `$rB + 32 > VM_MAX_RAM`
* Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

### COINBASE: Block proposer address

|             |                                  |
| ----------- | -------------------------------- |
| Description | Get block proposer address.      |
| Operation   | ```MEM[$rA, 32] = coinbase();``` |
| Syntax      | `coinbase $rA`                   |
| Encoding    | `0x00 rA - - -`                  |
| Notes       |                                  |

Panic if:
* `$rA + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)

### LOADCODE: Load code from an external contract

|             |                                                                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description | Copy `$rC` bytes of code starting at `$rB` for contract with ID equal to the 32 bytes in memory starting at `$rA` into memory starting at `$ssp`. |
| Operation   | ```MEM[$ssp, $rC] = code($rA, $rB, $rC);```                                                                                                       |
| Syntax      | `loadcode $rA, $rB, $rC`                                                                                                                          |
| Encoding    | `0x00 rA rB rC -`                                                                                                                                 |
| Notes       | If `$rC` is greater than the code size, zero bytes are filled in.                                                                                 |

Panic if:
* `$ssp + $rC` overflows
* `$rA + 32` overflows
* `$ssp + $rC > VM_MAX_RAM`
* `$rA + 32 > VM_MAX_RAM`
* `$ssp != $sp`
* `$ssp + $rC > $hp`
* `$rC > CONTRACT_MAX_SIZE`
* `$rC > MEM_MAX_ACCESS_SIZE`
* Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`

Increment `$hp->codesize`, `$ssp`, and `$sp` by `$rC` padded to word alignment.

This opcode can be used to concatenate the code of multiple contracts together. It can only be used when the stack area of the call frame is unused (i.e. prior to being used).

### LOG: Log event

|             |                                |
| ----------- | ------------------------------ |
| Description | Log an event. This is a no-op. |
| Operation   | ```log($rA, $rB, $rC, $rD);``` |
| Syntax      | `log $rA, $rB, $rC, $rD`       |
| Encoding    | `0x00 rA rB rC rD`             |
| Notes       |                                |

### REVERT: Revert

|             |                                                                       |
| ----------- | --------------------------------------------------------------------- |
| Description | Halt execution, reverting state changes and returning value in `$rA`. |
| Operation   | ```revert($rA);```                                                    |
| Syntax      | `revert $rA`                                                          |
| Encoding    | `0x00 rA - - -`                                                       |
| Notes       |                                                                       |

After a revert:
1. All [OutputContract](../protocol/tx_format.md#outputcontract) outputs will have the same `amount` and `stateRoot` as on initialization.
1. All [OutputVariable](../protocol/tx_format.md outputs#outputvariable) outputs will have `to` and `amount` of zero.
1. All [OutputContractConditional](../protocol/tx_format.md#outputcontractconditional) outputs will have `contractID`, `amount`, and `stateRoot` of zero.

### SLOADCODE: Load code from static list

|             |                                                                                                                 |
| ----------- | --------------------------------------------------------------------------------------------------------------- |
| Description | Copy `$rC` bytes of code starting at `$rB` for contract with static index `$rA` into memory starting at `$ssp`. |
| Operation   | ```MEM[$ssp, $rC] = scode($rA, $rB, $rC);```                                                                    |
| Syntax      | `sloadcode $rA, $rB, $rC`                                                                                       |
| Encoding    | `0x00 rA rB rC -`                                                                                               |
| Notes       | If `$rC` is greater than the code size, zero bytes are filled in.                                               |

Panic if:
* `$ssp + $rC` overflows
* `$ssp + $rC > VM_MAX_RAM`
* `$rA >= MAX_STATIC_CONTRACTS`
* `$rA` is greater than or equal to `staticContractsCount` for the contract with ID `MEM[$fp, 32]`
* `$ssp != $sp`
* `$ssp + $rC > $hp`
* `$rC > CONTRACT_MAX_SIZE`
* `$rC > MEM_MAX_ACCESS_SIZE`
* `$fp == 0` (in the script context)

Increment `$hp->codesize`, `$ssp`, and `$sp` by `$rC` padded to word alignment.

This opcode can be used to concatenate the code of multiple contracts together. It can only be used when the stack area of the call frame is unused (i.e. prior to being used).

### SRW: State read word

|             |                                                   |
| ----------- | ------------------------------------------------- |
| Description | A word is read from the current contract's state. |
| Operation   | ```$rA = STATE[MEM[$rB, 32]][0, 8];```            |
| Syntax      | `srw $rA, $rB`                                    |
| Encoding    | `0x00 rA rB - -`                                  |
| Notes       | Returns zero if the state element does not exist. |

Panic if:
* `$rA` is a [reserved register](./main.md#semantics)
* `$rB + 32` overflows
* `$rB + 32 > VM_MAX_RAM`
* `$fp == 0` (in the script context)

### SRWX: State read 32 bytes

|             |                                                     |
| ----------- | --------------------------------------------------- |
| Description | 32 bytes is read from the current contract's state. |
| Operation   | ```MEM[$rA, 32] = STATE[MEM[$rB, 32]];```           |
| Syntax      | `srwx $rA, $rB`                                     |
| Encoding    | `0x00 rA rB - -`                                    |
| Notes       | Returns zero if the state element does not exist.   |

Panic if:
* `$rA + 32` overflows
* `$rB + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rB + 32 > VM_MAX_RAM`
* The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
* `$fp == 0` (in the script context)

### SWW: State write word

|             |                                                    |
| ----------- | -------------------------------------------------- |
| Description | A word is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rA, 32]][0, 8] = $rB;```             |
| Syntax      | `sww $rA $rB`                                      |
| Encoding    | `0x00 rA rB - -`                                   |
| Notes       |                                                    |

Panic if:
* `$rA + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$fp == 0` (in the script context)

### SWWX: State write 32 bytes

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | 32 bytes is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rA, 32]] = MEM[$rB, 32];```            |
| Syntax      | `swwx $rA, $rB`                                      |
| Encoding    | `0x00 rA rB - -`                                     |
| Notes       |                                                      |

Panic if:
* `$rA + 32` overflows
* `$rB + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rB + 32 > VM_MAX_RAM`
* `$fp == 0` (in the script context)

### TRANSFER: Transfer coins to contract

|             |                                                                        |
| ----------- | ---------------------------------------------------------------------- |
| Description | Transfer `$rB` coins with color at `$rC` to contract with ID at `$rA`. |
| Operation   | ```transfer(MEM[$rA, 32], $rB, MEM[$rC, 32]);```                       |
| Syntax      | `transfer $rA, $rB, $rC`                                               |
| Encoding    | `0x00 rA rB rC -`                                                      |
| Notes       |                                                                        |

Given helper `balanceOfStart(color: byte[32]) -> uint32` which returns the memory address of `color` balance, or `0` if `color` has no balance.

Panic if:
* `$rA + 32` overflows
* `$rC + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rC + 32 > VM_MAX_RAM`
* Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
* In an external context, if `$rB > MEM[balanceOf(MEM[$rC, 32]), 8]`
* In an internal context, if `$rB` is greater than the balance of color `MEM[$rC, 32]` of output with contract ID `MEM[$fp, 32]`
* `$rB == 0`

For output with contract ID `MEM[$rA, 32]`, increase balance of color `MEM[$rC, 32]` by `$rB`. In an external context, decrease `MEM[balanceOfStart(MEM[$rC, 32]), 8]` by `$rB`. In an internal context, decrease color `MEM[$rC, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rB`.

This modifies the `balanceRoot` field of the appropriate output(s).

### TRANSFEROUT: Transfer coins to output

|             |                                                                                  |
| ----------- | -------------------------------------------------------------------------------- |
| Description | Transfer `$rC` coins with color at `$rD` to address at `$rA`, with output `$rB`. |
| Operation   | ```transferout(MEM[$rd, 32], $rs, $rt, MEM[$ru, 32]);```                         |
| Syntax      | `transferout $rA, $rB, $rC, $rD`                                                 |
| Encoding    | `0x00 rA rB rC rD`                                                               |
| Notes       |                                                                                  |

Given helper `balanceOfStart(color: byte[32]) -> uint32` which returns the memory address of `color` balance, or `0` if `color` has no balance.

Panic if:
* `$rA + 32` overflows
* `$rD + 32` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rD + 32 > VM_MAX_RAM`
* `$rB > tx.outputsCount`
* In an external context, if `$rC > MEM[balanceOf(MEM[$rD, 32]), 8]`
* In an internal context, if `$rC` is greater than the balance of color `MEM[$rD, 32]` of output with contract ID `MEM[$fp, 32]`
* `$rC == 0`
* `tx.outputs[$rB].type != OutputType.Variable`
* `tx.outputs[$rB].amount != 0`

In an external context, decrease `MEM[balanceOfStart(MEM[$rD, 32]), 8]` by `$rC`. In an internal context, decrease color `MEM[$rD, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rC`. Then set:
* `tx.outputs[$rB].to = MEM[$rA, 32]`
* `tx.outputs[$rB].amount = $rC`
* `tx.outputs[$rB].color = MEM[$rD, 32]`

This modifies the `balanceRoot` field of the appropriate output(s).

## Cryptographic Opcodes

### ECRECOVER: Signature recovery

|             |                                                                                                                             |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- |
| Description | The 64-byte public key (x, y) recovered from 64-byte signature starting at `$rB` on 32-byte message hash starting at `$rC`. |
| Operation   | ```MEM[$rA, 64] = ecrecover(MEM[$rB, 64], MEM[$rC, 32]);```                                                                 |
| Syntax      | `ecrecover $rA, $rB, $rC`                                                                                                   |
| Encoding    | `0x00 rA rB rC -`                                                                                                           |
| Notes       |                                                                                                                             |

Panic if:
* `$rA + 64` overflows
* `$rB + 64` overflows
* `$rC + 32` overflows
* `$rA + 64 > VM_MAX_RAM`
* `$rB + 64 > VM_MAX_RAM`
* `$rC + 32 > VM_MAX_RAM`
* The memory range `MEM[$rA, 64]`  does not pass [ownership check](./main.md#ownership)

To get the address, hash the public key with [SHA-2-256](#sha256-sha-2-256).

### KECCAK256: keccak-256

|             |                                                       |
| ----------- | ----------------------------------------------------- |
| Description | The keccak-256 hash of `$rC` bytes starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = keccak256(MEM[$rB, $rC]);```        |
| Syntax      | `keccak256 $rA, $rB, $rC`                             |
| Encoding    | `0x00 rA rB rC -`                                     |
| Notes       |                                                       |

Panic if:
* `$rA + 32` overflows
* `$rB + $rC` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rB + $rC > VM_MAX_RAM`
* The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
* `$rC > MEM_MAX_ACCESS_SIZE`

### SHA256: SHA-2-256

|             |                                                      |
| ----------- | ---------------------------------------------------- |
| Description | The SHA-2-256 hash of `$rC` bytes starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = sha256(MEM[$rB, $rC]);```          |
| Syntax      | `sha256 $rA, $rB, $rC`                               |
| Encoding    | `0x00 rA rB rC -`                                    |
| Notes       |                                                      |

Panic if:
* `$rA + 32` overflows
* `$rB + $rC` overflows
* `$rA + 32 > VM_MAX_RAM`
* `$rB + $rC > VM_MAX_RAM`
* The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
* `$rC > MEM_MAX_ACCESS_SIZE`

## Other Opcodes

### FLAG: Set flags

|             |                       |
| ----------- | --------------------- |
| Description | Set `$flag` to `$rA`. |
| Operation   | ```$flag = $rA;```    |
| Syntax      | `flag $rA`            |
| Encoding    | `0x00 rA - - -`       |
| Notes       |                       |
