# FuelVM Instruction Set

- [Reading Guide](#reading-guide)
- [Arithmetic/Logic (ALU) Instructions](#arithmeticlogic-alu-instructions)
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
  - [LT: Less than](#lt-less-than)
  - [MLOG: Math logarithm](#mlog-math-logarithm)
  - [MOD: Modulus](#mod-modulus)
  - [MODI: Modulus immediate](#modi-modulus-immediate)
  - [MOVE: Move](#move-move)
  - [MOVI: Move immediate](#movi-move-immediate)
  - [MROO: Math root](#mroo-math-root)
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
- [Control Flow Instructions](#control-flow-instructions)
  - [CIMV: Check input maturity verify](#cimv-check-input-maturity-verify)
  - [CTMV: Check transaction maturity verify](#ctmv-check-transaction-maturity-verify)
  - [JMP: Jump](#jmp-jump)
  - [JI: Jump immediate](#ji-jump-immediate)
  - [JNE: Jump if not equal](#jne-jump-if-not-equal)
  - [JNEI: Jump if not equal immediate](#jnei-jump-if-not-equal-immediate)
  - [JNZI: Jump if not zero immediate](#jnzi-jump-if-not-zero-immediate)
  - [RET: Return from context](#ret-return-from-context)
- [Memory Instructions](#memory-instructions)
  - [ALOC: Allocate memory](#aloc-allocate-memory)
  - [CFEI: Extend call frame immediate](#cfei-extend-call-frame-immediate)
  - [CFSI: Shrink call frame immediate](#cfsi-shrink-call-frame-immediate)
  - [LB: Load byte](#lb-load-byte)
  - [LW: Load word](#lw-load-word)
  - [MCL: Memory clear](#mcl-memory-clear)
  - [MCLI: Memory clear immediate](#mcli-memory-clear-immediate)
  - [MCP: Memory copy](#mcp-memory-copy)
  - [MCPI: Memory copy immediate](#mcpi-memory-copy-immediate)
  - [MEQ: Memory equality](#meq-memory-equality)
  - [SB: Store byte](#sb-store-byte)
  - [SW: Store word](#sw-store-word)
- [Contract Instructions](#contract-instructions)
  - [BAL: Balance of contract ID](#bal-balance-of-contract-id)
  - [BHEI: Block height](#bhei-block-height)
  - [BHSH: Block hash](#bhsh-block-hash)
  - [BURN: Burn existing coins](#burn-burn-existing-coins)
  - [CALL: Call contract](#call-call-contract)
  - [CB: Block proposer address](#cb-block-proposer-address)
  - [CCP: Code copy](#ccp-code-copy)
  - [CROO: Code Merkle root](#croo-code-merkle-root)
  - [CSIZ: Code size](#csiz-code-size)
  - [LDC: Load code from an external contract](#ldc-load-code-from-an-external-contract)
  - [LOG: Log event](#log-log-event)
  - [LOGD: Log data event](#logd-log-data-event)
  - [MINT: Mint new coins](#mint-mint-new-coins)
  - [RETD: Return from context with data](#retd-return-from-context-with-data)
  - [RVRT: Revert](#rvrt-revert)
  - [SRW: State read word](#srw-state-read-word)
  - [SRWQ: State read 32 bytes](#srwq-state-read-32-bytes)
  - [SWW: State write word](#sww-state-write-word)
  - [SWWQ: State write 32 bytes](#swwq-state-write-32-bytes)
  - [TR: Transfer coins to contract](#tr-transfer-coins-to-contract)
  - [TRO: Transfer coins to output](#tro-transfer-coins-to-output)
- [Cryptographic Instructions](#cryptographic-instructions)
  - [ECR: Signature recovery](#ecr-signature-recovery)
  - [K256: keccak-256](#k256-keccak-256)
  - [S256: SHA-2-256](#s256-sha-2-256)
- [Transaction Access Instructions](#transaction-access-instructions)
  - [XIL: Transaction input length](#xil-transaction-input-length)
  - [XIS: Transaction input start](#xis-transaction-input-start)
  - [XOL: Transaction output length](#xol-transaction-output-length)
  - [XOS: Transaction output start](#xos-transaction-output-start)
  - [XWL: Transaction witness length](#xwl-transaction-witness-length)
  - [XWS: Transaction witness start](#xws-transaction-witness-start)
- [Other Instructions](#other-instructions)
  - [FLAG: Set flags](#flag-set-flags)
  - [GM: Get metadata](#gm-get-metadata)

## Reading Guide

This page provides a description of all instructions for the FuelVM. Encoding is read as a sequence of one 8-bit value (the opcode identifier) followed by four 6-bit values (the register identifiers or immediate value). A single `i` indicates a 6-bit immediate value, `i i` indicates a 12-bit immediate value, `i i i` indicates an 18-bit immediate value, and `i i i i` indicates a 24-bit immediate value. All immediate values are interpreted as big-endian unsigned integers.

- The syntax `MEM[x, y]` used in this page means the memory range starting at byte `x`, of length `y` bytes.

Some instructions may _panic_, i.e. enter an unrecoverable state. Additionally, attempting to execute an instruction not in this list causes a panic and consumes no gas. How a panic is handled depends on [context](./main.md#contexts):

- In a predicate context, cease VM execution and return `false`.
- In other contexts, revert (described below).

On a non-predicate panic, append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Panic`                                                       |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

then append an additional receipt to the list of receipts, again modifying `tx.receiptsRoot`:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `1`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

## Arithmetic/Logic (ALU) Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

If the [`F_UNSAFEMATH`](./main.md#flags) flag is unset, an operation that would have set `$err` to `true` is instead a panic.

If the [`F_WRAPPING`](./main.md#flags) flag is unset, an operation that would have set `$of` to a non-zero value is instead a panic.

### ADD: Add

|             |                        |
|-------------|------------------------|
| Description | Adds two registers.    |
| Operation   | ```$rA = $rB + $rC;``` |
| Syntax      | `add $rA, $rB, $rC`    |
| Encoding    | `0x00 rA rB rC -`      |
| Notes       |                        |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### ADDI: Add immediate

|             |                                         |
|-------------|-----------------------------------------|
| Description | Adds a register and an immediate value. |
| Operation   | ```$rA = $rB + imm;```                  |
| Syntax      | `addi $rA, $rB, immediate`              |
| Encoding    | `0x00 rA rB i i`                        |
| Notes       |                                         |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### AND: AND

|             |                             |
|-------------|-----------------------------|
| Description | Bitwise ANDs two registers. |
| Operation   | ```$rA = $rB & $rC;```      |
| Syntax      | `and $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`           |
| Notes       |                             |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### ANDI: AND immediate

|             |                                                 |
|-------------|-------------------------------------------------|
| Description | Bitwise ANDs a register and an immediate value. |
| Operation   | ```$rA = $rB & imm;```                          |
| Syntax      | `andi $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                                |
| Notes       |                                                 |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`imm` is extended to 64 bits, with the high 52 bits set to `0`.

`$of` and `$err` are cleared.

### DIV: Divide

|             |                         |
|-------------|-------------------------|
| Description | Divides two registers.  |
| Operation   | ```$rA = $rB // $rC;``` |
| Syntax      | `div $rA, $rB, $rC`     |
| Encoding    | `0x00 rA rB rC -`       |
| Notes       |                         |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If `$rC == 0`, `$rA` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### DIVI: Divide immediate

|             |                                            |
|-------------|--------------------------------------------|
| Description | Divides a register and an immediate value. |
| Operation   | ```$rA = $rB // imm;```                    |
| Syntax      | `divi $rA, $rB, imm`                       |
| Encoding    | `0x00 rA rB i i`                           |
| Notes       |                                            |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If `imm == 0`, `$rA` is cleared and `$err` is set to `true`.

Otherwise, `$err` is cleared.

`$of` is cleared.

### EQ: Equals

|             |                                      |
|-------------|--------------------------------------|
| Description | Compares two registers for equality. |
| Operation   | ```$rA = $rB == $rC;```              |
| Syntax      | `eq $rA, $rB, $rC`                   |
| Encoding    | `0x00 rA rB rC -`                    |
| Notes       |                                      |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### EXP: Exponentiate

|             |                                              |
|-------------|----------------------------------------------|
| Description | Raises one register to the power of another. |
| Operation   | ```$rA = $rB ** $rC;```                      |
| Syntax      | `exp $rA, $rB, $rC`                          |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If the result cannot fit in 8 bytes, `$of` is set to `1` and `$rA` is instead set to `0`, otherwise `$of` is cleared.

`$err` is cleared.

### EXPI: Exponentiate immediate

|             |                                                         |
|-------------|---------------------------------------------------------|
| Description | Raises one register to the power of an immediate value. |
| Operation   | ```$rA = $rB ** imm;```                                 |
| Syntax      | `expi $rA, $rB, imm`                                    |
| Encoding    | `0x00 rA rB i i`                                        |
| Notes       |                                                         |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If the result cannot fit in 8 bytes, `$of` is set to `1` and `$rA` is instead set to `0`, otherwise `$of` is cleared.

`$err` is cleared.

### GT: Greater than

|             |                                          |
|-------------|------------------------------------------|
| Description | Compares two registers for greater-than. |
| Operation   | ```$rA = $rB > $rC;```                   |
| Syntax      | `gt $rA, $rB, $rC`                       |
| Encoding    | `0x00 rA rB rC -`                        |
| Notes       |                                          |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### LT: Less than

|             |                                       |
|-------------|---------------------------------------|
| Description | Compares two registers for less-than. |
| Operation   | ```$rA = $rB < $rC;```                |
| Syntax      | `lt $rA, $rB, $rC`                    |
| Encoding    | `0x00 rA rB rC -`                     |
| Notes       |                                       |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### MLOG: Math logarithm

|             |                                              |
|-------------|----------------------------------------------|
| Description | The (integer) logarithm base `$rC` of `$rB`. |
| Operation   | ```$rA = math.floor(math.log($rB, $rC));```  |
| Syntax      | `mlog $rA, $rB, $rC`                         |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If `$rB == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

If `$rC <= 1`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MOD: Modulus

|             |                                    |
|-------------|------------------------------------|
| Description | Modulo remainder of two registers. |
| Operation   | ```$rA = $rB % $rC;```             |
| Syntax      | `mod $rA, $rB, $rC`                |
| Encoding    | `0x00 rA rB rC -`                  |
| Notes       |                                    |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If `$rC == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MODI: Modulus immediate

|             |                                                        |
|-------------|--------------------------------------------------------|
| Description | Modulo remainder of a register and an immediate value. |
| Operation   | ```$rA = $rB % imm;```                                 |
| Syntax      | `modi $rA, $rB, imm`                                   |
| Encoding    | `0x00 rA rB i i`                                       |
| Notes       |                                                        |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If `imm == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MOVE: Move

|             |                                    |
|-------------|------------------------------------|
| Description | Copy from one register to another. |
| Operation   | ```$rA = $rB;```                   |
| Syntax      | `move $rA, $rB`                    |
| Encoding    | `0x00 rA rB - -`                   |
| Notes       |                                    |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### MOVI: Move immediate

|             |                                          |
|-------------|------------------------------------------|
| Description | Copy an immediate value into a register. |
| Operation   | ```$rA = imm;```                         |
| Syntax      | `movi $rA, imm`                          |
| Encoding    | `0x00 rA i i i`                          |
| Notes       |                                          |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### MROO: Math root

|             |                                              |
|-------------|----------------------------------------------|
| Description | The (integer) `$rC`th root of `$rB`.         |
| Operation   | ```$rA = math.floor(math.root($rB, $rC));``` |
| Syntax      | `mroo $rA, $rB, $rC`                         |
| Encoding    | `0x00 rA rB rC -`                            |
| Notes       |                                              |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

If `$rC == 0`, both `$rA` and `$of` are cleared and `$err` is set to `true`.

Otherwise, `$of` and `$err` are cleared.

### MUL: Multiply

|             |                           |
|-------------|---------------------------|
| Description | Multiplies two registers. |
| Operation   | ```$rA = $rB * $rC;```    |
| Syntax      | `mul $rA, $rB, $rC`       |
| Encoding    | `0x00 rA rB rC -`         |
| Notes       |                           |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### MULI: Multiply immediate

|             |                                               |
|-------------|-----------------------------------------------|
| Description | Multiplies a register and an immediate value. |
| Operation   | ```$rA = $rB * imm;```                        |
| Syntax      | `mul $rA, $rB, imm`                           |
| Encoding    | `0x00 rA rB i i`                              |
| Notes       |                                               |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the overflow of the operation.

`$err` is cleared.

### NOOP: No operation

|             |                        |
|-------------|------------------------|
| Description | Performs no operation. |
| Operation   |                        |
| Syntax      | `noop`                 |
| Encoding    | `0x00 - - - -`         |
| Notes       |                        |

`$of` and `$err` are cleared.

### NOT: Invert

|             |                         |
|-------------|-------------------------|
| Description | Bitwise NOT a register. |
| Operation   | ```$rA = ~$rB;```       |
| Syntax      | `not $rA, $rB`          |
| Encoding    | `0x00 rA rB - -`        |
| Notes       |                         |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### OR: OR

|             |                            |
|-------------|----------------------------|
| Description | Bitwise ORs two registers. |
| Operation   | ```$rA = $rB \| $rC;```    |
| Syntax      | `or $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`          |
| Notes       |                            |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### ORI: OR immediate

|             |                                                |
|-------------|------------------------------------------------|
| Description | Bitwise ORs a register and an immediate value. |
| Operation   | ```$rA = $rB \| imm;```                        |
| Syntax      | `ori $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                               |
| Notes       |                                                |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`imm` is extended to 64 bits, with the high 52 bits set to `0`.

`$of` and `$err` are cleared.

### SLL: Shift left logical

|             |                                       |
|-------------|---------------------------------------|
| Description | Left shifts a register by a register. |
| Operation   | ```$rA = $rB << $rC;```               |
| Syntax      | `sll $rA, $rB, $rC`                   |
| Encoding    | `0x00 rA rB rC -`                     |
| Notes       | Zeroes are shifted in.                |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### SLLI: Shift left logical immediate

|             |                                               |
|-------------|-----------------------------------------------|
| Description | Left shifts a register by an immediate value. |
| Operation   | ```$rA = $rB << imm;```                       |
| Syntax      | `slli $rA, $rB, imm`                          |
| Encoding    | `0x00 rA rB i i`                              |
| Notes       | Zeroes are shifted in.                        |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### SRL: Shift right logical

|             |                                        |
|-------------|----------------------------------------|
| Description | Right shifts a register by a register. |
| Operation   | ```$rA = $rB >> $rC;```                |
| Syntax      | `srl $rA, $rB, $rC`                    |
| Encoding    | `0x00 rA rB rC -`                      |
| Notes       | Zeroes are shifted in.                 |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SRLI: Shift right logical immediate

|             |                                                |
|-------------|------------------------------------------------|
| Description | Right shifts a register by an immediate value. |
| Operation   | ```$rA = $rB >> imm;```                        |
| Syntax      | `srli $rA, $rB, imm`                           |
| Encoding    | `0x00 rA rB i i`                               |
| Notes       | Zeroes are shifted in.                         |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SUB: Subtract

|             |                                                  |
|-------------|--------------------------------------------------|
| Description | Subtracts two registers.                         |
| Operation   | ```$rA = $rB - $rC;```                           |
| Syntax      | `sub $rA, $rB, $rC`                              |
| Encoding    | `0x00 rA rB rC -`                                |
| Notes       | `$of` is assigned the overflow of the operation. |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### SUBI: Subtract immediate

|             |                                                  |
|-------------|--------------------------------------------------|
| Description | Subtracts a register and an immediate value.     |
| Operation   | ```$rA = $rB - imm;```                           |
| Syntax      | `subi $rA, $rB, imm`                             |
| Encoding    | `0x00 rA rB i i`                                 |
| Notes       | `$of` is assigned the overflow of the operation. |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` is assigned the underflow of the operation, as though `$of` is the high byte of a 128-bit register.

`$err` is cleared.

### XOR: XOR

|             |                             |
|-------------|-----------------------------|
| Description | Bitwise XORs two registers. |
| Operation   | ```$rA = $rB ^ $rC;```      |
| Syntax      | `xor $rA, $rB, $rC`         |
| Encoding    | `0x00 rA rB rC -`           |
| Notes       |                             |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

### XORI: XOR immediate

|             |                                                 |
|-------------|-------------------------------------------------|
| Description | Bitwise XORs a register and an immediate value. |
| Operation   | ```$rA = $rB ^ imm;```                          |
| Syntax      | `xori $rA, $rB, imm`                            |
| Encoding    | `0x00 rA rB i i`                                |
| Notes       |                                                 |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

`$of` and `$err` are cleared.

## Control Flow Instructions

### CIMV: Check input maturity verify

|             |                                                             |
|-------------|-------------------------------------------------------------|
| Description | Set `$rA` to `true` if the `$rC <= tx.input[$rB].maturity`. |
| Operation   | ```$rA = checkinputmaturityverify($rB, $rC);```             |
| Syntax      | `cimv $rA $rB $rC`                                          |
| Encoding    | `0x00 rA rB rC -`                                           |
| Notes       |                                                             |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rC > tx.input[$rB].maturity`
- the input `$rB` is not of type [`InputType.Coin`](../protocol/tx_format.md)
- `$rB > tx.inputsCount`

Otherwise, advance the program counter `$pc` by `4`.

See also: [BIP-112](https://github.com/bitcoin/bips/blob/master/bip-0112.mediawiki) and [CLTV](#cltv-check-lock-time-verify).

### CTMV: Check transaction maturity verify

|             |                                                  |
|-------------|--------------------------------------------------|
| Description | Set `$rA` to `true` if `$rB <= tx.maturity`.     |
| Operation   | ```$rA = checktransactionmaturityverify($rB);``` |
| Syntax      | `ctmv $rA $rB`                                   |
| Encoding    | `0x00 rA rB - -`                                 |
| Notes       |                                                  |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB > tx.maturity`

Otherwise, advance the program counter `$pc` by `4`.

See also: [BIP-65](https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki) and [Bitcoin's Time Locks](https://prestwi.ch/bitcoin-time-locks).

### JMP: Jump

|             |                                                     |
|-------------|-----------------------------------------------------|
| Description | Jumps to the code instruction offset by a register. |
| Operation   | ```$pc = $is + $rA * 4;```                          |
| Syntax      | `jmp $rA`                                           |
| Encoding    | `0x00 rA - - -`                                     |
| Notes       |                                                     |

Panic if:

- `$is + $rA * 4 > VM_MAX_RAM - 1`

### JI: Jump immediate

|             |                                                |
|-------------|------------------------------------------------|
| Description | Jumps to the code instruction offset by `imm`. |
| Operation   | ```$pc = $is + imm * 4;```                     |
| Syntax      | `ji imm`                                       |
| Encoding    | `0x00 i i i i`                                 |
| Notes       |                                                |

Panic if:

- `$is + imm * 4 > VM_MAX_RAM - 1`

### JNE: Jump if not equal

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Jump to the code instruction offset by a register if `$rA` is not equal to `$rB`.    |
| Operation   | ```if $rA != $rB:```<br>```$pc = $is + $rC * 4;```<br>```else:```<br>```$pc += 4;``` |
| Syntax      | `jne $rA $rB $rC`                                                                    |
| Encoding    | `0x00 rA rB rC -`                                                                    |
| Notes       |                                                                                      |

Panic if:

- `$is + $rC * 4 > VM_MAX_RAM - 1`

### JNEI: Jump if not equal immediate

|             |                                                                                      |
|-------------|--------------------------------------------------------------------------------------|
| Description | Jump to the code instruction offset by `imm` if `$rA` is not equal to `$rB`.         |
| Operation   | ```if $rA != $rB:```<br>```$pc = $is + imm * 4;```<br>```else:```<br>```$pc += 4;``` |
| Syntax      | `jnei $rA $rB imm`                                                                   |
| Encoding    | `0x00 rA rB i i`                                                                     |
| Notes       |                                                                                      |

Panic if:

- `$is + imm * 4 > VM_MAX_RAM - 1`

### JNZI: Jump if not zero immediate

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Jump to the code instruction offset by `imm` if `$rA` is not equal to `$zero`.         |
| Operation   | ```if $rA != $zero:```<br>```$pc = $is + imm * 4;```<br>```else:```<br>```$pc += 4;``` |
| Syntax      | `jnzi $rA imm`                                                                         |
| Encoding    | `0x00 rA i i i`                                                                        |
| Notes       |                                                                                        |

Panic if:

- `$is + imm * 4 > VM_MAX_RAM - 1`

### RET: Return from context

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | Returns from [context](./main.md#contexts) with value `$rA`. |
| Operation   | ```return($rA);```                                           |
| Syntax      | `ret $rA`                                                    |
| Encoding    | `0x00 rA - - -`                                              |
| Notes       |                                                              |

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Return`                                                      |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val`  | `uint64`      | Value of register `$rA`.                                                  |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

If current context is external, append an additional receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `0`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

If current context is external, cease VM execution and return `$rA`.

Returns from contract call, popping the call frame. Before popping perform the following operations.

Return the unused forwarded gas to the caller:

1. `$cgas = $cgas + $fp->$cgas` (add remaining context gas from previous context to current remaining context gas)

Set the return value:

1. `$ret = $rA`
1. `$retl = 0`

Then pop the call frame and restoring registers _except_ `$ggas`, `$cgas`, `$ret`, and `$retl`. Afterwards, set the following registers:

1. `$pc = $pc + 4` (advance program counter from where we called)

## Memory Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### ALOC: Allocate memory

|             |                                           |
|-------------|-------------------------------------------|
| Description | Allocate a number of bytes from the heap. |
| Operation   | ```$hp = $hp - $rA;```                    |
| Syntax      | `aloc $rA`                                |
| Encoding    | `0x00 rA - - -`                           |
| Notes       | Does not initialize memory.               |

Panic if:

- `$hp - $rA` underflows
- `$hp - $rA < $sp`

### CFEI: Extend call frame immediate

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | Extend the current call frame's stack by an immediate value. |
| Operation   | ```$sp = $sp + imm```                                        |
| Syntax      | `cfei imm`                                                   |
| Encoding    | `0x00 i i i i`                                               |
| Notes       | Does not initialize memory.                                  |

Panic if:

- `$sp + imm` overflows
- `$sp + imm > $hp`

### CFSI: Shrink call frame immediate

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | Shrink the current call frame's stack by an immediate value. |
| Operation   | ```$sp = $sp - imm```                                        |
| Syntax      | `cfsi imm`                                                   |
| Encoding    | `0x00 i i i i`                                               |
| Notes       | Does not clear memory.                                       |

Panic if:

- `$sp - imm` underflows
- `$sp - imm < $ssp`

### LB: Load byte

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | A byte is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + imm, 1];```                               |
| Syntax      | `lb $rA, $rB, imm`                                           |
| Encoding    | `0x00 rA rB i i`                                             |
| Notes       |                                                              |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB + imm + 1` overflows
- `$rB + imm + 1 > VM_MAX_RAM`

### LW: Load word

|             |                                                              |
|-------------|--------------------------------------------------------------|
| Description | A word is loaded from the specified address offset by `imm`. |
| Operation   | ```$rA = MEM[$rB + (imm * 8), 8];```                         |
| Syntax      | `lw $rA, $rB, imm`                                           |
| Encoding    | `0x00 rA rB i i`                                             |
| Notes       |                                                              |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB + (imm * 8) + 8` overflows
- `$rB + (imm * 8) + 8 > VM_MAX_RAM`

### MCL: Memory clear

|             |                          |
|-------------|--------------------------|
| Description | Clear bytes in memory.   |
| Operation   | ```MEM[$rA, $rB] = 0;``` |
| Syntax      | `mcl $rA, $rB`           |
| Encoding    | `0x00 rA rB - -`         |
| Notes       |                          |

Panic if:

- `$rA + $rB` overflows
- `$rA + $rB > VM_MAX_RAM`
- `$rB > MEM_MAX_ACCESS_SIZE`
- The memory range `MEM[$rA, $rB]`  does not pass [ownership check](./main.md#ownership)

### MCLI: Memory clear immediate

|             |                          |
|-------------|--------------------------|
| Description | Clear bytes in memory.   |
| Operation   | ```MEM[$rA, imm] = 0;``` |
| Syntax      | `mcli $rA, imm`          |
| Encoding    | `0x00 rA i i i`          |
| Notes       |                          |

Panic if:

- `$rA + imm` overflows
- `$rA + imm > VM_MAX_RAM`
- `imm > MEM_MAX_ACCESS_SIZE`
- The memory range `MEM[$rA, imm]`  does not pass [ownership check](./main.md#ownership)

### MCP: Memory copy

|             |                                      |
|-------------|--------------------------------------|
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rA, $rC] = MEM[$rB, $rC];``` |
| Syntax      | `mcp $rA, $rB, $rC`                  |
| Encoding    | `0x00 rA rB rC -`                    |
| Notes       |                                      |

Panic if:

- `$rA + $rC` overflows
- `$rB + $rC` overflows
- `$rA + $rC > VM_MAX_RAM`
- `$rB + $rC > VM_MAX_RAM`
- `$rC > MEM_MAX_ACCESS_SIZE`
- The memory ranges `MEM[$rA, $rC]` and `MEM[$rB, $rC]` overlap
- The memory range `MEM[$rA, $rC]`  does not pass [ownership check](./main.md#ownership)

### MCPI: Memory copy immediate

|             |                                      |
|-------------|--------------------------------------|
| Description | Copy bytes in memory.                |
| Operation   | ```MEM[$rA, imm] = MEM[$rB, imm];``` |
| Syntax      | `mcpi $rA, $rB, imm`                 |
| Encoding    | `0x00 rA rB imm imm`                 |
| Notes       |                                      |

Panic if:

- `$rA + imm` overflows
- `$rB + imm` overflows
- `$rA + imm > VM_MAX_RAM`
- `$rB + imm > VM_MAX_RAM`
- `imm > MEM_MAX_ACCESS_SIZE`
- The memory ranges `MEM[$rA, imm]` and `MEM[$rB, imm]` overlap
- The memory range `MEM[$rA, imm]`  does not pass [ownership check](./main.md#ownership)

### MEQ: Memory equality

|             |                                             |
|-------------|---------------------------------------------|
| Description | Compare bytes in memory.                    |
| Operation   | ```$rA = MEM[$rB, $rD] == MEM[$rC, $rD];``` |
| Syntax      | `meq $rA, $rB, $rC, $rD`                    |
| Encoding    | `0x00 rA rB rC rD`                          |
| Notes       |                                             |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB + $rD` overflows
- `$rC + $rD` overflows
- `$rB + $rD > VM_MAX_RAM`
- `$rC + $rD > VM_MAX_RAM`
- `$rD > MEM_MAX_ACCESS_SIZE`

### SB: Store byte

|             |                                                                                     |
|-------------|-------------------------------------------------------------------------------------|
| Description | The least significant byte of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + imm, 1] = $rB[7, 1];```                                                |
| Syntax      | `sb $rA, $rB, imm`                                                                  |
| Encoding    | `0x00 rA rB i i`                                                                    |
| Notes       |                                                                                     |

Panic if:

- `$rA + imm + 1` overflows
- `$rA + imm + 1 > VM_MAX_RAM`
- The memory range `MEM[$rA + imm, 1]`  does not pass [ownership check](./main.md#ownership)

### SW: Store word

|             |                                                                    |
|-------------|--------------------------------------------------------------------|
| Description | The value of `$rB` is stored at the address `$rA` offset by `imm`. |
| Operation   | ```MEM[$rA + (imm * 8), 8] = $rB;```                               |
| Syntax      | `sw $rA, $rB, imm`                                                 |
| Encoding    | `0x00 rA rB i i`                                                   |
| Notes       |                                                                    |

Panic if:

- `$rA + (imm * 8) + 8` overflows
- `$rA + (imm * 8) + 8 > VM_MAX_RAM`
- The memory range `MEM[$rA + (imm * 8), 8]`  does not pass [ownership check](./main.md#ownership)

## Contract Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation, except for [CALL](#call-call-contract), [RETD](#retd-return-from-context-with-data) and [RVRT](#rvrt-revert).

### BAL: Balance of contract ID

|             |                                                                              |
|-------------|------------------------------------------------------------------------------|
| Description | Set `$rA` to the balance of asset ID at `$rB` for contract with ID at `$rC`. |
| Operation   | ```$rA = balance(MEM[$rB, 32], MEM[$rC, 32]);```                             |
| Syntax      | `bal $rA, $rB, $rC`                                                          |
| Encoding    | `0x00 rA rB rC -`                                                            |
| Notes       |                                                                              |

Where helper `balance(asset_id: byte[32], contract_id: byte[32]) -> uint64` returns the current balance of `asset_id` of contract with ID `contract_id`.

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB + 32` overflows
- `$rB + 32 > VM_MAX_RAM`
- `$rC + 32` overflows
- `$rC + 32 > VM_MAX_RAM`
- Contract with ID `MEM[$rC, 32]` is not in `tx.inputs`

### BHEI: Block height

|             |                            |
|-------------|----------------------------|
| Description | Get Fuel block height.     |
| Operation   | ```$rA = blockheight();``` |
| Syntax      | `bhei $rA`                 |
| Encoding    | `0x00 rA - - -`            |
| Notes       |                            |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)

### BHSH: Block hash

|             |                                      |
|-------------|--------------------------------------|
| Description | Get block header hash.               |
| Operation   | ```MEM[$rA, 32] = blockhash($rB);``` |
| Syntax      | `bhsh $rA $rB`                       |
| Encoding    | `0x00 rA rB - -`                     |
| Notes       |                                      |

Panic if:

- `$rA + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)

Block header hashes for blocks with height greater than or equal to current block height are zero (```0x00**32```).

### BURN: Burn existing coins

|             |                                                      |
|-------------|------------------------------------------------------|
| Description | Burn `$rA` coins of the current contract's asset ID. |
| Operation   | ```burn($rA);```                                     |
| Syntax      | `burn $rA`                                           |
| Encoding    | `0x00 rA - - -`                                      |
| Notes       |                                                      |

Panic if:

- Balance of asset ID `MEM[$fp, 32]` of output with contract ID `MEM[$fp, 32]` minus `$rA` underflows
- `$fp == 0` (in the script context)

For output with contract ID `MEM[$fp, 32]`, decrease balance of asset ID `MEM[$fp, 32]` by `$rA`.

This modifies the `balanceRoot` field of the appropriate output.

### CALL: Call contract

|             |                        |
|-------------|------------------------|
| Description | Call contract.         |
| Operation   |                        |
| Syntax      | `call $rA $rB $rC $rD` |
| Encoding    | `0x00 rA rB rC rD`     |
| Notes       |                        |

Given helper `balanceOfStart(asset_id: byte[32]) -> uint32` which returns the memory address of the remaining free balance of `asset_id`, or panics if `asset_id` has no free balance remaining.

Panic if:

- `$rA + 32` overflows
- `$rC + 32` overflows
- Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
- Reading past `MEM[VM_MAX_RAM - 1]`
- In an external context, if `$rB > MEM[balanceOfStart(MEM[$rC, 32]), 8]`
- In an internal context, if `$rB` is greater than the balance of asset ID `MEM[$rC, 32]` of output with contract ID `MEM[$fp, 32]`

Register `$rA` is a memory address from which the following fields are set (word-aligned):

| bytes | type       | value    | description          |
|-------|------------|----------|----------------------|
| 32    | `byte[32]` | `to`     | Contract ID to call. |
| 8     | `byte[8]`  | `param1` | First parameter.     |
| 8     | `byte[8]`  | `param2` | Second parameter.    |

`$rB` is the amount of coins to forward. `$rC` points to the 32-byte asset ID of the coins to forward. `$rD` is the amount of gas to forward. If it is set to an amount greater than the available gas, all available gas is forwarded.

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name       | type          | description                                                               |
|------------|---------------|---------------------------------------------------------------------------|
| `type`     | `ReceiptType` | `ReceiptType.Call`                                                        |
| `from`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `to`       | `byte[32]`    | Contract ID of called contract.                                           |
| `amount`   | `uint64`      | Amount of coins to forward, i.e. `$rB`.                                   |
| `asset_id` | `byte[32]`    | Asset ID of coins to forward, i.e. `MEM[$rC, 32]`.                        |
| `gas`      | `uint64`      | Gas to forward, i.e. `$rD`.                                               |
| `param1`   | `uint64`      | First parameter.                                                          |
| `param2`   | `uint64`      | Second parameter.                                                         |
| `pc`       | `uint64`      | Value of register `$pc`.                                                  |
| `is`       | `uint64`      | Value of register `$is`.                                                  |

For output with contract ID `MEM[$rA, 32]`, increase balance of asset ID `MEM[$rC, 32]` by `$rB`. In an external context, decrease `MEM[balanceOfStart(MEM[$rC, 32]), 8]` by `$rB`. In an internal context, decrease asset ID `MEM[$rC, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rB`.

A [call frame](./main.md#call-frames) is pushed at `$sp`. In addition to filling in the values of the call frame, the following registers are set:

1. `$fp = $sp` (on top of the previous call frame is the beginning of this call frame)
1. Set `$ssp` and `$sp` to the start of the writable stack area of the call frame.
1. Set `$pc` and `$is` to the starting address of the code.
1. `$bal = $rB` (forward coins)
1. `$cgas = $rD` or all available gas (forward gas)

This modifies the `balanceRoot` field of the appropriate output(s).

### CB: Block proposer address

|             |                                  |
|-------------|----------------------------------|
| Description | Get block proposer address.      |
| Operation   | ```MEM[$rA, 32] = coinbase();``` |
| Syntax      | `cb $rA`                         |
| Encoding    | `0x00 rA - - -`                  |
| Notes       |                                  |

Panic if:

- `$rA + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)

### CCP: Code copy

|             |                                                                                                                                                  |
|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| Description | Copy `$rD` bytes of code starting at `$rC` for contract with ID equal to the 32 bytes in memory starting at `$rB` into memory starting at `$rA`. |
| Operation   | ```MEM[$rA, $rD] = code($rB, $rC, $rD);```                                                                                                       |
| Syntax      | `ccp $rA, $rB, $rC, $rD`                                                                                                                         |
| Encoding    | `0x00 rA rB rC rD`                                                                                                                               |
| Notes       | If `$rD` is greater than the code size, zero bytes are filled in.                                                                                |

This is used only for reading and inspecting code of other contracts.
Use [`LDC`](#ldc-load-code-from-an-external-contract) to load code for executing.

Panic if:

- `$rA + $rD` overflows
- `$rB + 32` overflows
- `$rA + $rD > VM_MAX_RAM`
- `$rB + 32 > VM_MAX_RAM`
- The memory range `MEM[$rA, $rD]`  does not pass [ownership check](./main.md#ownership)
- `$rD > MEM_MAX_ACCESS_SIZE`
- Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

### CROO: Code Merkle root

|             |                                                                                                                                       |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Description | Set the 32 bytes in memory starting at `$rA` to the code root for contract with ID equal to the 32 bytes in memory starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = coderoot(MEM[$rB, 32]);```                                                                                          |
| Syntax      | `croo $rA, $rB`                                                                                                                       |
| Encoding    | `0x00 rA rB - -`                                                                                                                      |
| Notes       |                                                                                                                                       |

Panic if:

- `$rA + 32` overflows
- `$rB + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rB + 32 > VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
- Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

Code root computation is defined [here](../protocol/identifiers.md#contract-id).

### CSIZ: Code size

|             |                                                                                                           |
|-------------|-----------------------------------------------------------------------------------------------------------|
| Description | Set `$rA` to the size of the code for contract with ID equal to the 32 bytes in memory starting at `$rB`. |
| Operation   | ```$rA = codesize(MEM[$rB, 32]);```                                                                       |
| Syntax      | `csiz $rA, $rB`                                                                                           |
| Encoding    | `0x00 rA rB - -`                                                                                          |
| Notes       |                                                                                                           |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB + 32` overflows
- `$rB + 32 > VM_MAX_RAM`
- Contract with ID `MEM[$rB, 32]` is not in `tx.inputs`

### LDC: Load code from an external contract

|             |                                                                                                                                                   |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| Description | Copy `$rC` bytes of code starting at `$rB` for contract with ID equal to the 32 bytes in memory starting at `$rA` into memory starting at `$ssp`. |
| Operation   | ```MEM[$ssp, $rC] = code($rA, $rB, $rC);```                                                                                                       |
| Syntax      | `ldc $rA, $rB, $rC`                                                                                                                               |
| Encoding    | `0x00 rA rB rC -`                                                                                                                                 |
| Notes       | If `$rC` is greater than the code size, zero bytes are filled in.                                                                                 |

Panic if:

- `$ssp + $rC` overflows
- `$rA + 32` overflows
- `$ssp + $rC > VM_MAX_RAM`
- `$rA + 32 > VM_MAX_RAM`
- `$ssp != $sp`
- `$ssp + $rC > $hp`
- `$rC > CONTRACT_MAX_SIZE`
- `$rC > MEM_MAX_ACCESS_SIZE`
- Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`

Increment `$fp->codesize`, `$ssp`, and `$sp` by `$rC` padded to word alignment.

This instruction can be used to concatenate the code of multiple contracts together. It can only be used when the stack area of the call frame is unused (i.e. prior to being used).

### LOG: Log event

|             |                                |
|-------------|--------------------------------|
| Description | Log an event. This is a no-op. |
| Operation   | ```log($rA, $rB, $rC, $rD);``` |
| Syntax      | `log $rA, $rB, $rC, $rD`       |
| Encoding    | `0x00 rA rB rC rD`             |
| Notes       |                                |

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Log`                                                         |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val0` | `uint64`      | Value of register `$rA`.                                                  |
| `val1` | `uint64`      | Value of register `$rB`.                                                  |
| `val2` | `uint64`      | Value of register `$rC`.                                                  |
| `val3` | `uint64`      | Value of register `$rD`.                                                  |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

### LOGD: Log data event

|             |                                 |
|-------------|---------------------------------|
| Description | Log an event. This is a no-op.  |
| Operation   | ```logd($rA, $rB, $rC, $rD);``` |
| Syntax      | `logd $rA, $rB, $rC, $rD`       |
| Encoding    | `0x00 rA rB rC rD`              |
| Notes       |                                 |

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name     | type          | description                                                               |
|----------|---------------|---------------------------------------------------------------------------|
| `type`   | `ReceiptType` | `ReceiptType.LogData`                                                     |
| `id`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val0`   | `uint64`      | Value of register `$rA`.                                                  |
| `val1`   | `uint64`      | Value of register `$rB`.                                                  |
| `ptr`    | `uint64`      | Value of register `$rC`.                                                  |
| `len`    | `uint64`      | Value of register `$rD`.                                                  |
| `digest` | `byte[32]`    | [Hash](#s256-sha-2-256) of `MEM[$rC, $rD]`.                               |
| `pc`     | `uint64`      | Value of register `$pc`.                                                  |
| `is`     | `uint64`      | Value of register `$is`.                                                  |

Logs the memory range `MEM[$rC, $rD]`.

### MINT: Mint new coins

|             |                                                      |
|-------------|------------------------------------------------------|
| Description | Mint `$rA` coins of the current contract's asset ID. |
| Operation   | ```mint($rA);```                                     |
| Syntax      | `mint $rA`                                           |
| Encoding    | `0x00 rA - - -`                                      |
| Notes       |                                                      |

Panic if:

- Balance of asset ID `MEM[$fp, 32]` of output with contract ID `MEM[$fp, 32]` plus `$rA` overflows
- `$fp == 0` (in the script context)

For output with contract ID `MEM[$fp, 32]`, increase balance of asset ID `MEM[$fp, 32]` by `$rA`.

This modifies the `balanceRoot` field of the appropriate output.

### RETD: Return from context with data

|             |                                                                        |
|-------------|------------------------------------------------------------------------|
| Description | Returns from [context](./main.md#contexts) with value `MEM[$rA, $rB]`. |
| Operation   | ```returndata($rA, $rB);```                                            |
| Syntax      | `retd $rA, $rB`                                                        |
| Encoding    | `0x00 rA rB - -`                                                       |
| Notes       |                                                                        |

Panic if:

- `$rA + $rB` overflows
- `$rA + $rB > VM_MAX_RAM`
- `$rB > MEM_MAX_ACCESS_SIZE`

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name     | type          | description                                                               |
|----------|---------------|---------------------------------------------------------------------------|
| `type`   | `ReceiptType` | `ReceiptType.ReturnData`                                                  |
| `id`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `ptr`    | `uint64`      | Value of register `$rA`.                                                  |
| `len`    | `uint64`      | Value of register `$rB`.                                                  |
| `digest` | `byte[32]`    | [Hash](#s256-sha-2-256) of `MEM[$rA, $rB]`.                               |
| `pc`     | `uint64`      | Value of register `$pc`.                                                  |
| `is`     | `uint64`      | Value of register `$is`.                                                  |

If current context is a script, append an additional receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `0`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

If current context is external, cease VM execution and return `MEM[$rA, $rB]`.

Returns from contract call, popping the call frame. Before popping, perform the following operations.

Return the unused forwarded gas to the caller:

1. `$cgas = $cgas + $fp->$cgas` (add remaining context gas from previous context to current remaining context gas)

Set the return value:

1. `$ret = $rA`
1. `$retl = $rB`

Then pop the call frame and restoring registers _except_ `$ggas`, `$cgas`, `$ret`, and `$retl`. Afterwards, set the following registers:

1. `$pc = $pc + 4` (advance program counter from where we called)

### RVRT: Revert

|             |                                                                       |
|-------------|-----------------------------------------------------------------------|
| Description | Halt execution, reverting state changes and returning value in `$rA`. |
| Operation   | ```revert($rA);```                                                    |
| Syntax      | `rvrt $rA`                                                            |
| Encoding    | `0x00 rA - - -`                                                       |
| Notes       |                                                                       |

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name   | type          | description                                                               |
|--------|---------------|---------------------------------------------------------------------------|
| `type` | `ReceiptType` | `ReceiptType.Revert`                                                      |
| `id`   | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `val`  | `uint64`      | Value of register `$rA`.                                                  |
| `pc`   | `uint64`      | Value of register `$pc`.                                                  |
| `is`   | `uint64`      | Value of register `$is`.                                                  |

Then append an additional receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name       | type          | description                 |
|------------|---------------|-----------------------------|
| `type`     | `ReceiptType` | `ReceiptType.ScriptResult`  |
| `result`   | `uint64`      | `1`                         |
| `gas_used` | `uint64`      | Gas consumed by the script. |

Cease VM execution and revert script effects. After a revert:

1. All [OutputContract](../protocol/tx_format.md#outputcontract) outputs will have the same `balanceRoot` and `stateRoot` as on initialization.
1. All [OutputVariable](../protocol/tx_format.md#outputvariable) outputs will have `to`, `amount`, and `asset_id` of zero.

### SRW: State read word

|             |                                                   |
|-------------|---------------------------------------------------|
| Description | A word is read from the current contract's state. |
| Operation   | ```$rA = STATE[MEM[$rB, 32]][0, 8];```            |
| Syntax      | `srw $rA, $rB`                                    |
| Encoding    | `0x00 rA rB - -`                                  |
| Notes       | Returns zero if the state element does not exist. |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB + 32` overflows
- `$rB + 32 > VM_MAX_RAM`
- `$fp == 0` (in the script context)

### SRWQ: State read 32 bytes

|             |                                                     |
|-------------|-----------------------------------------------------|
| Description | 32 bytes is read from the current contract's state. |
| Operation   | ```MEM[$rA, 32] = STATE[MEM[$rB, 32]];```           |
| Syntax      | `srwq $rA, $rB`                                     |
| Encoding    | `0x00 rA rB - -`                                    |
| Notes       | Returns zero if the state element does not exist.   |

Panic if:

- `$rA + 32` overflows
- `$rB + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rB + 32 > VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
- `$fp == 0` (in the script context)

### SWW: State write word

|             |                                                                                 |
|-------------|---------------------------------------------------------------------------------|
| Description | A word is written to the current contract's state.                              |
| Operation   | ```STATE[MEM[$rA, 32]][0, 8] = $rB;```<br>```STATE[MEM[$rA, 32]][8, 24] = 0;``` |
| Syntax      | `sww $rA $rB`                                                                   |
| Encoding    | `0x00 rA rB - -`                                                                |
| Notes       |                                                                                 |

Panic if:

- `$rA + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$fp == 0` (in the script context)

The last 24 bytes of `STATE[MEM[$rA, 32]]` are set to `0`.

### SWWQ: State write 32 bytes

|             |                                                      |
|-------------|------------------------------------------------------|
| Description | 32 bytes is written to the current contract's state. |
| Operation   | ```STATE[MEM[$rA, 32]] = MEM[$rB, 32];```            |
| Syntax      | `swwq $rA, $rB`                                      |
| Encoding    | `0x00 rA rB - -`                                     |
| Notes       |                                                      |

Panic if:

- `$rA + 32` overflows
- `$rB + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rB + 32 > VM_MAX_RAM`
- `$fp == 0` (in the script context)

### TR: Transfer coins to contract

|             |                                                                           |
|-------------|---------------------------------------------------------------------------|
| Description | Transfer `$rB` coins with asset ID at `$rC` to contract with ID at `$rA`. |
| Operation   | ```transfer(MEM[$rA, 32], $rB, MEM[$rC, 32]);```                          |
| Syntax      | `tr $rA, $rB, $rC`                                                        |
| Encoding    | `0x00 rA rB rC -`                                                         |
| Notes       |                                                                           |

Given helper `balanceOfStart(asset_id: byte[32]) -> uint32` which returns the memory address of the remaining free balance of `asset_id`, or panics if `asset_id` has no free balance remaining.

Panic if:

- `$rA + 32` overflows
- `$rC + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rC + 32 > VM_MAX_RAM`
- Contract with ID `MEM[$rA, 32]` is not in `tx.inputs`
- In an external context, if `$rB > MEM[balanceOfStart(MEM[$rC, 32]), 8]`
- In an internal context, if `$rB` is greater than the balance of asset ID `MEM[$rC, 32]` of output with contract ID `MEM[$fp, 32]`
- `$rB == 0`

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name       | type          | description                                                               |
|------------|---------------|---------------------------------------------------------------------------|
| `type`     | `ReceiptType` | `ReceiptType.Transfer`                                                    |
| `from`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `to`       | `byte[32]`    | Contract ID of contract to transfer coins to.                             |
| `amount`   | `uint64`      | Amount of coins transferred.                                              |
| `asset_id` | `byte[32]`    | asset ID of coins transferred.                                            |
| `pc`       | `uint64`      | Value of register `$pc`.                                                  |
| `is`       | `uint64`      | Value of register `$is`.                                                  |

For output with contract ID `MEM[$rA, 32]`, increase balance of asset ID `MEM[$rC, 32]` by `$rB`. In an external context, decrease `MEM[balanceOfStart(MEM[$rC, 32]), 8]` by `$rB`. In an internal context, decrease asset ID `MEM[$rC, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rB`.

This modifies the `balanceRoot` field of the appropriate output(s).

### TRO: Transfer coins to output

|             |                                                                                     |
|-------------|-------------------------------------------------------------------------------------|
| Description | Transfer `$rC` coins with asset ID at `$rD` to address at `$rA`, with output `$rB`. |
| Operation   | ```transferout(MEM[$rA, 32], $rB, $rC, MEM[$rD, 32]);```                            |
| Syntax      | `tro $rA, $rB, $rC, $rD`                                                            |
| Encoding    | `0x00 rA rB rC rD`                                                                  |
| Notes       |                                                                                     |

Given helper `balanceOfStart(asset_id: byte[32]) -> uint32` which returns the memory address of the remaining free balance of `asset_id`, or panics if `asset_id` has no free balance remaining.

Panic if:

- `$rA + 32` overflows
- `$rD + 32` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rD + 32 > VM_MAX_RAM`
- `$rB > tx.outputsCount`
- In an external context, if `$rC > MEM[balanceOfStart(MEM[$rD, 32]), 8]`
- In an internal context, if `$rC` is greater than the balance of asset ID `MEM[$rD, 32]` of output with contract ID `MEM[$fp, 32]`
- `$rC == 0`
- `tx.outputs[$rB].type != OutputType.Variable`
- `tx.outputs[$rB].amount != 0`

Append a receipt to the list of receipts, modifying `tx.receiptsRoot`:

| name       | type          | description                                                               |
|------------|---------------|---------------------------------------------------------------------------|
| `type`     | `ReceiptType` | `ReceiptType.TransferOut`                                                 |
| `from`     | `byte[32]`    | Contract ID of current context if in an internal context, zero otherwise. |
| `to`       | `byte[32]`    | Address to transfer coins to.                                             |
| `amount`   | `uint64`      | Amount of coins transferred.                                              |
| `asset_id` | `byte[32]`    | asset ID of coins transferred.                                            |
| `pc`       | `uint64`      | Value of register `$pc`.                                                  |
| `is`       | `uint64`      | Value of register `$is`.                                                  |

In an external context, decrease `MEM[balanceOfStart(MEM[$rD, 32]), 8]` by `$rC`. In an internal context, decrease asset ID `MEM[$rD, 32]` balance of output with contract ID `MEM[$fp, 32]` by `$rC`. Then set:

- `tx.outputs[$rB].to = MEM[$rA, 32]`
- `tx.outputs[$rB].amount = $rC`
- `tx.outputs[$rB].asset_id = MEM[$rD, 32]`

This modifies the `balanceRoot` field of the appropriate output(s).

## Cryptographic Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### ECR: Signature recovery

|             |                                                                                                                             |
|-------------|-----------------------------------------------------------------------------------------------------------------------------|
| Description | The 64-byte public key (x, y) recovered from 64-byte signature starting at `$rB` on 32-byte message hash starting at `$rC`. |
| Operation   | ```MEM[$rA, 64] = ecrecover(MEM[$rB, 64], MEM[$rC, 32]);```                                                                 |
| Syntax      | `ecr $rA, $rB, $rC`                                                                                                         |
| Encoding    | `0x00 rA rB rC -`                                                                                                           |
| Notes       |                                                                                                                             |

Panic if:

- `$rA + 64` overflows
- `$rB + 64` overflows
- `$rC + 32` overflows
- `$rA + 64 > VM_MAX_RAM`
- `$rB + 64 > VM_MAX_RAM`
- `$rC + 32 > VM_MAX_RAM`
- The memory range `MEM[$rA, 64]` does not pass [ownership check](./main.md#ownership)

Signatures and signature verification are specified [here](../protocol/cryptographic_primitives.md#public-key-cryptography).

If the signature cannot be verified, `MEM[$rA, 64]` is set to `0` and `$err` is set to `1`, otherwise `$err` is cleared.

To get the address from the public key, hash the public key with [SHA-2-256](#sha256-sha-2-256).

### K256: keccak-256

|             |                                                       |
|-------------|-------------------------------------------------------|
| Description | The keccak-256 hash of `$rC` bytes starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = keccak256(MEM[$rB, $rC]);```        |
| Syntax      | `k256 $rA, $rB, $rC`                                  |
| Encoding    | `0x00 rA rB rC -`                                     |
| Notes       |                                                       |

Panic if:

- `$rA + 32` overflows
- `$rB + $rC` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rB + $rC > VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
- `$rC > MEM_MAX_ACCESS_SIZE`

### S256: SHA-2-256

|             |                                                      |
|-------------|------------------------------------------------------|
| Description | The SHA-2-256 hash of `$rC` bytes starting at `$rB`. |
| Operation   | ```MEM[$rA, 32] = sha256(MEM[$rB, $rC]);```          |
| Syntax      | `s256 $rA, $rB, $rC`                                 |
| Encoding    | `0x00 rA rB rC -`                                    |
| Notes       |                                                      |

Panic if:

- `$rA + 32` overflows
- `$rB + $rC` overflows
- `$rA + 32 > VM_MAX_RAM`
- `$rB + $rC > VM_MAX_RAM`
- The memory range `MEM[$rA, 32]`  does not pass [ownership check](./main.md#ownership)
- `$rC > MEM_MAX_ACCESS_SIZE`

## Transaction Access Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### XIL: Transaction input length

|             |                                                                                       |
|-------------|---------------------------------------------------------------------------------------|
| Description | Set `$rA` to the length in bytes of [the `$rB`th input](./main.md#vm-initialization). |
| Operation   | ```$rA = xil($rB);```                                                                 |
| Syntax      | `xil $rA, $rB`                                                                        |
| Encoding    | `0x00 rA rB - -`                                                                      |
| Notes       |                                                                                       |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB >= tx.inputsCount`

### XIS: Transaction input start

|             |                                                                                                   |
|-------------|---------------------------------------------------------------------------------------------------|
| Description | Set `$rA` to the memory address of the start of [the `$rB`th input](./main.md#vm-initialization). |
| Operation   | ```$rA = xis($rB);```                                                                             |
| Syntax      | `xis $rA, $rB`                                                                                    |
| Encoding    | `0x00 rA rB - -`                                                                                  |
| Notes       |                                                                                                   |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB >= tx.inputsCount`

### XOL: Transaction output length

|             |                                                                                        |
|-------------|----------------------------------------------------------------------------------------|
| Description | Set `$rA` to the length in bytes of [the `$rB`th output](./main.md#vm-initialization). |
| Operation   | ```$rA = xol($rB);```                                                                  |
| Syntax      | `xol $rA, $rB`                                                                         |
| Encoding    | `0x00 rA rB - -`                                                                       |
| Notes       |                                                                                        |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB >= tx.outputsCount`

### XOS: Transaction output start

|             |                                                                                                    |
|-------------|----------------------------------------------------------------------------------------------------|
| Description | Set `$rA` to the memory address of the start of [the `$rB`th output](./main.md#vm-initialization). |
| Operation   | ```$rA = xos($rB);```                                                                              |
| Syntax      | `xos $rA, $rB`                                                                                     |
| Encoding    | `0x00 rA rB - -`                                                                                   |
| Notes       |                                                                                                    |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB >= tx.outputsCount`

### XWL: Transaction witness length

|             |                                                                                         |
|-------------|-----------------------------------------------------------------------------------------|
| Description | Set `$rA` to the length in bytes of [the `$rB`th witness](./main.md#vm-initialization). |
| Operation   | ```$rA = xwl($rB);```                                                                   |
| Syntax      | `xwl $rA, $rB`                                                                          |
| Encoding    | `0x00 rA rB - -`                                                                        |
| Notes       |                                                                                         |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB >= tx.witnessesCount`

Note that the returned length includes the [_entire_ witness](../protocol/tx_format.md), not just of the witness's `data` field.

### XWS: Transaction witness start

|             |                                                                                                     |
|-------------|-----------------------------------------------------------------------------------------------------|
| Description | Set `$rA` to the memory address of the start of [the `$rB`th witness](./main.md#vm-initialization). |
| Operation   | ```$rA = xws($rB);```                                                                               |
| Syntax      | `xws $rA, $rB`                                                                                      |
| Encoding    | `0x00 rA rB - -`                                                                                    |
| Notes       |                                                                                                     |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- `$rB >= tx.witnessesCount`

Note that the returned memory address includes the [_entire_ witness](../protocol/tx_format.md), not just of the witness's `data` field.

## Other Instructions

All these instructions advance the program counter `$pc` by `4` after performing their operation.

### FLAG: Set flags

|             |                       |
|-------------|-----------------------|
| Description | Set `$flag` to `$rA`. |
| Operation   | ```$flag = $rA;```    |
| Syntax      | `flag $rA`            |
| Encoding    | `0x00 rA - - -`       |
| Notes       |                       |

### GM: Get metadata

|             |                           |
|-------------|---------------------------|
| Description | Get metadata from memory. |
| Operation   | Varies (see below).       |
| Syntax      | `gm $rA, imm`             |
| Encoding    | `0x00 rA imm imm imm`     |
| Notes       |                           |

Read metadata from memory. A convenience instruction to avoid manually extracting metadata.

| name                    | value     | description                |
|-------------------------|-----------|----------------------------|
| `GM_IS_CALLER_EXTERNAL` | `0x00001` | Get if caller is external. |
| `GM_GET_CALLER`         | `0x00002` | Get caller's contract ID.  |

If `imm == GM_IS_CALLER_EXTERNAL`:

Panic if:

- `$fp == 0` (in an external context)

Set `$rA` to `true` if parent is an external context, `false` otherwise.

If `imm == GM_GET_CALLER`:

Panic if:

- `$fp == 0` (in an external context)
- `$fp->$fp == 0` (if parent context is external)

Set `$rA` to `$fp->$fp` (i.e. `$rA` will point to the previous call frame's contract ID).

### GT: Get transaction fields

|             |                         |
|-------------|-------------------------|
| Description | Get transaction fields. |
| Operation   | Varies (see below).     |
| Syntax      | `gt $rA, $rB, imm`      |
| Encoding    | `0x00 rA rB i i`        |
| Notes       |                         |

Get [fields from the transaction](../protocol/tx_format.md#transaction).

| name                                     | value   | set `$rA` to                                     |
|------------------------------------------|---------|--------------------------------------------------|
| `GT_TYPE`                                | `0x001` | `tx.type`                                        |
| `GT_SCRIPT_GAS_PRICE`                    | `0x002` | `tx.gasPrice`                                    |
| `GT_SCRIPT_GAS_LIMIT`                    | `0x003` | `tx.gasLimit`                                    |
| `GT_SCRIPT_MATURITY`                     | `0x004` | `tx.maturity`                                    |
| `GT_SCRIPT_SCRIPT_LENGTH`                | `0x005` | `tx.scriptLength`                                |
| `GT_SCRIPT_SCRIPT_DATA_LENGTH`           | `0x006` | `tx.scriptDataLength`                            |
| `GT_SCRIPT_INPUTS_COUNT`                 | `0x007` | `tx.inputsCount`                                 |
| `GT_SCRIPT_OUTPUTS_COUNT`                | `0x008` | `tx.outputsCount`                                |
| `GT_SCRIPT_WITNESSES_COUNT`              | `0x009` | `tx.witnessesCount`                              |
| `GT_SCRIPT_RECEIPTS_ROOT`                | `0x00A` | Memory address of `tx.receiptsRoot`              |
| `GT_SCRIPT_SCRIPT`                       | `0x00B` | Memory address of `tx.script`                    |
| `GT_SCRIPT_SCRIPT_DATA`                  | `0x00C` | Memory address of `tx.scriptData`                |
| `GT_SCRIPT_INPUT_AT_INDEX`               | `0x00D` | Memory address of `tx.inputs[$rB]`               |
| `GT_SCRIPT_OUTPUT_AT_INDEX`              | `0x00E` | Memory address of `t.outputs[$rB]`               |
| `GT_SCRIPT_WITNESS_AT_INDEX`             | `0x00F` | Memory address of `tx.witnesses[$rB]`            |
| `GT_CREATE_GAS_PRICE`                    | `0x010` | `tx.gasPrice`                                    |
| `GT_CREATE_GAS_LIMIT`                    | `0x011` | `tx.gasLimit`                                    |
| `GT_CREATE_MATURITY`                     | `0x012` | `tx.maturity`                                    |
| `GT_CREATE_BYTECODE_LENGTH`              | `0x013` | `tx.bytecodeLength`                              |
| `GT_CREATE_BYTECODE_WITNESS_INDEX`       | `0x014` | `tx.bytecodeWitnessIndex`                        |
| `GT_CREATE_STORAGE_SLOTS_COUNT`          | `0x015` | `tx.storageSlotsCount`                           |
| `GT_CREATE_INPUTS_COUNT`                 | `0x016` | `tx.inputsCount`                                 |
| `GT_CREATE_OUTPUTS_COUNT`                | `0x017` | `tx.outputsCount`                                |
| `GT_CREATE_WITNESSES_COUNT`              | `0x018` | `tx.witnessesCount`                              |
| `GT_CREATE_SALT`                         | `0x019` | Memory address of `tx.salt`                      |
| `GT_CREATE_STORAGE_SLOT_AT_INDEX`        | `0x01A` | Memory address of `tx.storageSlots[$rB]`         |
| `GT_CREATE_INPUT_AT_INDEX`               | `0x01B` | Memory address of `tx.inputs[$rB]`               |
| `GT_CREATE_OUTPUT_AT_INDEX`              | `0x01C` | Memory address of `t.outputs[$rB]`               |
| `GT_CREATE_WITNESS_AT_INDEX`             | `0x01D` | Memory address of `tx.witnesses[$rB]`            |
| `GT_INPUT_TYPE`                          | `0x101` | `tx.inputs[$rB].type`                            |
| `GT_INPUT_COIN_TX_ID`                    | `0x102` | Memory address of `tx.inputs[$rB].txID`          |
| `GT_INPUT_COIN_OUTPUT_INDEX`             | `0x103` | `tx.inputs[$rB].outputIndex`                     |
| `GT_INPUT_COIN_OWNER`                    | `0x104` | Memory address of `tx.inputs[$rB].owner`         |
| `GT_INPUT_COIN_AMOUNT`                   | `0x105` | `tx.inputs[$rB].amount`                          |
| `GT_INPUT_COIN_ASSET_ID`                 | `0x106` | Memory address of `tx.inputs[$rB].asset_id`      |
| `GT_INPUT_COIN_TX_POINTER`               | `0x107` | Memory address of `tx.inputs[$rB].txPointer`     |
| `GT_INPUT_COIN_WITNESS_INDEX`            | `0x108` | `tx.inputs[$rB].witnessIndex`                    |
| `GT_INPUT_COIN_MATURITY`                 | `0x109` | `tx.inputs[$rB].maturity`                        |
| `GT_INPUT_COIN_PREDICATE_LENGTH`         | `0x10A` | `tx.inputs[$rB].predicateLength`                 |
| `GT_INPUT_COIN_PREDICATE_DATA_LENGTH`    | `0x10B` | `tx.inputs[$rB].predicateDataLength`             |
| `GT_INPUT_COIN_PREDICATE`                | `0x10C` | Memory address of `tx.inputs[$rB].predicate`     |
| `GT_INPUT_COIN_PREDICATE_DATA`           | `0x10D` | Memory address of `tx.inputs[$rB].predicateData` |
| `GT_INPUT_CONTRACT_TX_ID`                | `0x10E` | Memory address of `tx.inputs[$rB].txID`          |
| `GT_INPUT_CONTRACT_OUTPUT_INDEX`         | `0x10F` | `tx.inputs[$rB].outputIndex`                     |
| `GT_INPUT_CONTRACT_BALANCE_ROOT`         | `0x110` | Memory address of `tx.inputs[$rB].balanceRoot`   |
| `GT_INPUT_CONTRACT_STATE_ROOT`           | `0x111` | Memory address of `tx.inputs[$rB].stateRoot`     |
| `GT_INPUT_CONTRACT_TX_POINTER`           | `0x112` | Memory address of `tx.inputs[$rB].txPointer`     |
| `GT_INPUT_CONTRACT_CONTRACT_ID`          | `0x113` | Memory address of `tx.inputs[$rB].contractID`    |
| `GT_INPUT_MESSAGE_MESSAGE_ID`            | `0x114` | Memory address of `tx.inputs[$rB].messageID`     |
| `GT_INPUT_MESSAGE_SENDER`                | `0x115` | Memory address of `tx.inputs[$rB].sender`        |
| `GT_INPUT_MESSAGE_RECIPIENT`             | `0x116` | Memory address of `tx.inputs[$rB].recipient`     |
| `GT_INPUT_MESSAGE_AMOUNT`                | `0x117` | `tx.inputs[$rB].amount`                          |
| `GT_INPUT_MESSAGE_NONCE`                 | `0x118` | Memory address of `tx.inputs[$rB].nonce`         |
| `GT_INPUT_MESSAGE_OWNER`                 | `0x119` | Memory address of `tx.inputs[$rB].owner`         |
| `GT_INPUT_MESSAGE_WITNESS_INDEX`         | `0x11A` | `tx.inputs[$rB].witnessIndex`                    |
| `GT_INPUT_MESSAGE_DATA_LENGTH`           | `0x11B` | `tx.inputs[$rB].dataLength`                      |
| `GT_INPUT_MESSAGE_PREDICATE_LENGTH`      | `0x11C` | `tx.inputs[$rB].predicateLength`                 |
| `GT_INPUT_MESSAGE_PREDICATE_DATA_LENGTH` | `0x11D` | `tx.inputs[$rB].predicateDataLength`             |
| `GT_INPUT_MESSAGE_DATA`                  | `0x11E` | Memory address of `tx.inputs[$rB].data`          |
| `GT_INPUT_MESSAGE_PREDICATE`             | `0x11F` | Memory address of `tx.inputs[$rB].predicate`     |
| `GT_INPUT_MESSAGE_PREDICATE_DATA`        | `0x120` | Memory address of `tx.inputs[$rB].predicateData` |
| `GT_OUTPUT_TYPE`                         | `0x201` | `tx.outputs[$rB].type`                           |
| `GT_OUTPUT_COIN_TO`                      | `0x202` | Memory address of `tx.outputs[$rB].to`           |
| `GT_OUTPUT_COIN_AMOUNT`                  | `0x203` | `tx.outputs[$rB].amount`                         |
| `GT_OUTPUT_COIN_ASSET_ID`                | `0x204` | Memory address of `tx.outputs[$rB].asset_id`     |
| `GT_OUTPUT_CONTRACT_INPUT_INDEX`         | `0x205` | `tx.outputs[$rB].inputIndex`                     |
| `GT_OUTPUT_CONTRACT_BALANCE_ROOT`        | `0x206` | Memory address of `tx.outputs[$rB].balanceRoot`  |
| `GT_OUTPUT_CONTRACT_STATE_ROOT`          | `0x207` | Memory address of `tx.outputs[$rB].stateRoot`    |
| `GT_OUTPUT_MESSAGE_RECIPIENT`            | `0x208` | Memory address of `tx.outputs[$rB].recipient`    |
| `GT_OUTPUT_MESSAGE_AMOUNT`               | `0x209` | `tx.outputs[$rB].amount`                         |
| `GT_OUTPUT_CONTRACT_CREATED_CONTRACT_ID` | `0x20A` | Memory address of `tx.outputs[$rB].contractID`   |
| `GT_OUTPUT_CONTRACT_CREATED_STATE_ROOT`  | `0x20B` | Memory address of `tx.outputs[$rB].stateRoot`    |
| `GT_WITNESS_DATA_LENGTH`                 | `0x301` | `tx.witnesses[$rB].dataLength`                   |
| `GT_WITNESS_DATA`                        | `0x302` | Memory address of `tx.witnesses[$rB].data`       |

Panic if:

- `$rA` is a [reserved register](./main.md#semantics)
- The value of `$rB` results in an out of bounds access for variable-length fields
- The input or output type does not match (`OutputChange` and `OutputVariable` count as `OutputCoin`)
