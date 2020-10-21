# Fuel VM Specification

This document gives the Fuel VM specification. 

The specification covers the types, opcodes, and execution semantics.

## Types

At this iteration, we only consider a 256-bit type, i.e., we do not distinguish between bytes and (signed/unsigned) integers. This may change as the specification evolves.

## Opcodes

This section describes the Fuel VM's opcodes. 

Many opcodes are derived from the EVM opcodes specified in the [Ethereum Yellow Paper](https://github.com/ethereum/yellowpaper).

Additional opcodes are defined for the support of optimistic rollups and performance optimizations.

[STOP](#op-STOP)

## Format 

The instruction format is defined as follows:

* opcode: 8 bits
* destination register: 4 bits
* source register: 4 bits
* source register: 4 bits
* immediate values: 16 bits

In addition, there are some special registers defined as follows:

* $z: zero-containing register
* $hi: register containing high bits of multiplication/division result; remainder in div
* $lo: register containing low bits of multiplication/division result; divisor in div
* $of: bit-sized register indicating overflow
* $uf: bit-sized register indicating underflow
* $state: 2-bit sized register indicating statemachine status: RUNNING, HALTED


##### <a name='op-STOP'/>STOP: stop

| | |
|---|---|
| Description: | Halts execution of the VM |
| Operation: | STOP |
| Syntax: | stop |
| Encoding: | 00000000 - - - - - - |
| Notes: |  |


##### ADD: Add

| | |
|---|---|
| Description: | Adds two registers and stores the result in a register. |
| Operation: | ADD |
| Syntax: | add $rd, $rs, $rt |
| Encoding: | 00000001 rd rs rt - - - |
| Notes: | w overflow |

##### ADDI: Add immediate

| | |
|---|---|
| Description: | Adds a register and a sign-extended immediate value and stores the result in a register. |
| Operation: | ADDI |
| Syntax: | addi $rd, $rs, immediate |
| Encoding: | 00000010 rs rt i i i i |
| Notes: | w overflow |

##### ADDIU: Add immediate unsigned

| | |
|---|---|
| Description: | Adds a register and a sign-extended immediate value and stores the result in a register. |
| Operation: | ADDIU |
| Syntax: | addiu $rd, $rs, immediate |
| Encoding: | 00000011 rs rt i i i i |
| Notes: | no overflow |

##### ADDU: Add variable unsigned

| | |
|---|---|
| Description: | Adds two registers and stores the result in a register. |
| Operation: | ADDU |
| Syntax: | add $rd, $rs, $rt |
| Encoding: | 00000100 rd rs rt - - - |
| Notes: | no overflow |


##### AND: And

| | |
|---|---|
| Description: | Bitwise ands two registers and stores the result in a register. |
| Operation: | AND |
| Syntax: | and $rd, $rs, $rt |
| Encoding: | 00000101 rd rs rt - - - |
| Notes: |  |

##### ANDI: And immediate

| | |
|---|---|
| Description: | Bitwise ands a register and an immediate value and stores the result in a register. |
| Operation: | ANDI |
| Syntax: | andi $rd, $rs, imm |
| Encoding: | 00000110 rd rs - - - - |
| Notes: |  |


##### BEQ: Branch on equal

| | |
|---|---|
| Description: | Branches if the two registers are equal  |
| Operation: | BEQ |
| Syntax: | beq $rs, $rt, offset  |
| Encoding: | 00000111 rs rt i i i i |
| Notes: |  |

##### BGEZ: Branch on greater than or equal to zero

| | |
|---|---|
| Description: | Branches if the register is greater than or equal to zero  |
| Operation: | BGEZ |
| Syntax: | bgez $rs, offset  |
| Encoding: | 00001000 rs - i i i i |
| Notes: |  |

##### BGEZAL: Branch on greater than or equal to zero and link

| | |
|---|---|
| Description: | Branches if the register is greater than or equal to zero and saves the return address in $31  |
| Operation: | BGEZAL |
| Syntax: | bgezal $rs, offset  |
| Encoding: | 00001001 rs - i i i i |
| Notes: |  |

##### BGTZ: Branch on greater than zero

| | |
|---|---|
| Description: | Branches if the register is greater than zero  |
| Operation: | BGTZ |
| Syntax: | bgtz $rs, offset  |
| Encoding: | 00001010 rs - i i i i |
| Notes: |  |

##### BLEZ: Branch on less than on equal to zero

| | |
|---|---|
| Description: | Branches if the register is less than or equal to zero  |
| Operation: | BLEZ |
| Syntax: | blez $rs, offset  |
| Encoding: | 00001011 rs - i i i i |
| Notes: |  |

##### BLTZ: Branch on less than zero

| | |
|---|---|
| Description: | Branches if the register is less than zero  |
| Operation: | BLTZ |
| Syntax: | bltz $rs, offset  |
| Encoding: | 00001100 rs - i i i i |
| Notes: |  |

##### BLTZAL: Branch on less than zero and link

| | |
|---|---|
| Description: | Branches if the register is less than zero and saves the return address in $31 |
| Operation: | BLTZAL |
| Syntax: | bltzal $rs, offset  |
| Encoding: | 00001101 rs - i i i i |
| Notes: |  |

##### BNE: Branch on not equal

| | |
|---|---|
| Description: | Branches if the two registers are not equal |
| Operation: | BNE |
| Syntax: | bne $rs, $rt, offset  |
| Encoding: | 00001110 rs rt i i i i |
| Notes: |  |


##### DIV: Divide

| | |
|---|---|
| Description: | Divides $rs by $rt and stores the quotient in $lo and the remainder in $hi  |
| Operation: | DIV |
| Syntax: | div $rs, $rt |
| Encoding: | 00001111 rs rt - - - - |
| Notes: |  |

##### DIVU: Divide unsigned

| | |
|---|---|
| Description: | Divides $rs by $rt and stores the quotient in $lo and the remainder in $hi  |
| Operation: | DIVU |
| Syntax: | divu $rs, $rt |
| Encoding: | 00010000 rs rt - - - - |
| Notes: |  |


##### J: JUMP

| | |
|---|---|
| Description: | Jumps to the calculated address  |
| Operation: | J |
| Syntax: | j target |
| Encoding: | 00010001 i i i i i i |
| Notes: |  |


##### JAL: jump and link

| | |
|---|---|
| Description: | Jumps to the calculated address and stores the return address in $31  |
| Operation: | JAL |
| Syntax: | jal target |
| Encoding: | 00010010 i i i i i i |
| Notes: | register $31 used as link target |

##### JR: jump register

| | |
|---|---|
| Description: | Jump to the address contained in register $rs |
| Operation: | JR |
| Syntax: | jr $rs |
| Encoding: | 00010011 rs - - - - - |
| Notes: |  |


##### LB: load byte

| | |
|---|---|
| Description: | A byte is loaded into a register from the specified address  |
| Operation: | LB |
| Syntax: | lb $rt, offset($rs) |
| Encoding: | 00010100 rs rt i i i i |
| Notes: |  |

##### LUI: load upper immediate

| | |
|---|---|
| Description: | The immediate value is shifted left 16 bits and stored in the register. The lower 16 bits are zeroes. |
| Operation: | LUI |
| Syntax: | lui $rt, imm |
| Encoding: | 00010101 - rt i i i i |
| Notes: |  |

##### LW: load word

| | |
|---|---|
| Description: | A word is loaded into a register from the specified address  |
| Operation: | LW |
| Syntax: | lw $rt, offset($rs) |
| Encoding: | 00010110 rs rt i i i i |
| Notes: |  |


##### MFHI: Move from HI to register

| | |
|---|---|
| Description: | The contents of register $hi are moved to the specified register.  |
| Operation: | MFHI |
| Syntax: | mfhi $rd |
| Encoding: | 00010111 rd - - - - - |
| Notes: |  |

##### MFLO: Move from LO to register

| | |
|---|---|
| Description: | The contents of register $lo are moved to the specified register. |
| Operation: | MFLO |
| Syntax: | mflo $rd |
| Encoding: | 00011000 rd - - - - - |
| Notes: |  |


##### MULT: multiply signed

| | |
|---|---|
| Description: | Multiplies $rs by $rt and stores the result in $lo.  |
| Operation: | MULT |
| Syntax: | mult $rd, $rs, $rt |
| Encoding: | 00011001 rd rs rt - - - |
| Notes: | with overflow |

##### MULTU: multiply unsigned

| | |
|---|---|
| Description: | Multiplies $rs by $rt and stores the result in $lo.  |
| Operation: | MULTU |
| Syntax: | mult $rd, $rs, $rt |
| Encoding: | 00011010 rd rs rt - - - |
| Notes: | no overflow |


##### NOOP: no operation

| | |
|---|---|
| Description: | Performs no operation.  |
| Operation: | NOOP |
| Syntax: | noop |
| Encoding: | 00011011 - - - - - |
| Notes: |  |


##### OR: or

| | |
|---|---|
| Description: | Bitwise logical ors registers given by $rs and $rt and stores the result in $rd register. |
| Operation: | OR |
| Syntax: | or $rd, $rs, $rt |
| Encoding: | 00011100 rd rs rt - - - |
| Notes: |  |

##### ORI: or immediate

| | |
|---|---|
| Description: | Bitwise ors register $rs and an immediate value and stores the result in $rd register |
| Operation: | ORI |
| Syntax: | ori $rd, $rs, imm |
| Encoding: | 00011101 rd rs i i i i |
| Notes: |  |


##### SB: store byte

| | |
|---|---|
| Description: | The least significant byte of $rt is stored at the specified address.  |
| Operation: | SB |
| Syntax: | sb $rt, offset($rs) |
| Encoding: | 00011110 rs rt i i i i |
| Notes: |  |


##### SLL: shift left logical

| | |
|---|---|
| Description: | Shifts $rs register value left by the shift amount imm and places the result in $rd register. Zeroes are shifted in. |
| Operation: | SLL |
| Syntax: | sll $rd, $rs, imm |
| Encoding: | 00011111 rd rs i i i i |
| Notes: |  |

##### SLLV: shift left logical variable

| | |
|---|---|
| Description: | Shifts $rs register value left by the value in $rt register and places the result in $rd. Zeroes are shifted in.  |
| Operation: | SLLV |
| Syntax: | sllv $rd, $rs, $rt |
| Encoding: | 00100000 rd rs rt - - - |
| Notes: |  |


##### SLT: set if less than signed

| | |
|---|---|
| Description: | If $rs is less than $rt, $rd is set to one. It gets zero otherwise.  |
| Operation: | SLT |
| Syntax: | slt $rd, $rs, $rt |
| Encoding: | 00100001 rd rs rt - - - |
| Notes: |  |

##### SLTI: set if less than immediate signed

| | |
|---|---|
| Description: | If $rs is less than immediate, $rt is set to one. It gets zero otherwise.  |
| Operation: | SLTI |
| Syntax: | slti $rs, $rt, imm |
| Encoding: | 00100010 rs rt i i i i |
| Notes: |  |

##### SLTIU: set if less than immediate unsigned

| | |
|---|---|
| Description: | If $rs is less than the unsigned immediate, $rt is set to one. It gets zero otherwise.  |
| Operation: | SLTIU |
| Syntax: | sltiu $rs, $rt, imm |
| Encoding: | 00100011 rs rt i i i i |
| Notes: |  |

##### SLTU: set if less than unsigned

| | |
|---|---|
| Description: | If $rs is less than $rt, $rd is set to one. It gets zero otherwise.  |
| Operation: | SLTU |
| Syntax: | sltu $rd, $rs, $rt |
| Encoding: | 00100100 rd rs rt - - - |
| Notes: |  |


##### SRA: shift right arithmetic

| | |
|---|---|
| Description: | Shifts a register value right by the shift amount (imm) and places the value in the destination register. The sign bit is shifted in.  |
| Operation: | SRA |
| Syntax: | sra $rd, $rs, imm |
| Encoding: | 00100101 rd rs i i i i |
| Notes: | signed, sign bit is shifted in. MIPS uses shamt field in instruction instead of imm. |

##### SRL: shift right logical

| | |
|---|---|
| Description: | Shifts a register value right by the shift amount (imm) and places the value in the destination register. Zeroes are shifted in.  |
| Operation: | SRL |
| Syntax: | srl $rd, $rs, imm |
| Encoding: | 00100110 rd rs i i i i |
| Notes: | unsigned, zero is shifted in. MIPS uses shamt field in instruction instead of imm. |

##### SRLV: shift right logical variable

| | |
|---|---|
| Description: | Shifts a register value right by the amount specified in $rs and places the value in the destination register. Zeroes are shifted in.  |
| Operation: | SRLV |
| Syntax: | srlv $rd, $rs, $rt |
| Encoding: | 00100111 rd rs rt - - - |
| Notes: | unsigned, zero is shifted in |

##### SRAV: shift right arithmetic variable

| | |
|---|---|
| Description: | Shifts a register value right by the amount specified in $rs and places the value in the destination register. The sign bit is shifted in.  |
| Operation: | SRAV |
| Syntax: | srav $rd, $rs, $rt |
| Encoding: | 00101000 rd rs rt - - - |
| Notes: | signed, sign bit is shifted in. Not part of MIPS instructions. |


##### SUB: subtract

| | |
|---|---|
| Description: | Subtracts two registers and stores the result in a register |
| Operation: | SUB |
| Syntax: | sub $rd, $rs, $rt |
| Encoding: | 00101001 rd rs rt - - - |
| Notes: |  |

##### SUBU: subtract unsigned

| | |
|---|---|
| Description: | Subtracts two registers and stores the result in a register  |
| Operation: | SUBU |
| Syntax: | subu $rd, $rs, $rt |
| Encoding: | 00101010 rd rs rt - - - |
| Notes: |  |


##### SW: store word

| | |
|---|---|
| Description: | The contents of $rt is stored at the specified address.  |
| Operation: | SW |
| Syntax: | sw $rd, offset($rs) |
| Encoding: | 00101011 rd rs i i i i |
| Notes: |  |


##### SYSCALL: 

| | |
|---|---|
| Description: |  |
| Operation: | SYSCALL |
| Syntax: |  |
| Encoding: |   |
| Notes: |  |


##### XOR: xor

| | |
|---|---|
| Description: | Exclusive ors two registers and stores the result in a register. |
| Operation: | XOR |
| Syntax: | xor $rd, $rs, $rt  |
| Encoding: | 00101101 rd rs rt - - - |
| Notes: |  |

##### XORI: xor immediate

| | |
|---|---|
| Description: | Bitwise exclusive ors a register and an immediate value and stores the result in a register. |
| Operation: | XORI |
| Syntax: | xori $rt, $rs, imm  |
| Encoding: | 00101110 rs rt i i i i |
| Notes: |  |


##### opcode: Short label

| | |
|---|---|
| Description: | semantics/description |
| Operation: | opcode |
| Syntax: | syntax |
| Encoding: | opcode bit encoding |
| Notes: |  |


##### GAS: 

| | |
|---|---|
| Description: | Sets the $rd register value to the numeric value of gas remaining. |
| Operation: | GAS |
| Syntax: | gas $rd |
| Encoding: | 00101111 rd - - - - - |
| Notes: | EVM component |

##### BALANCE: 

| | |
|---|---|
| Description: | Takes the $rs register value, which is an address, and then sets the $rd register value to the balance of given address. |
| Operation: | BALANCE |
| Syntax: | balance $rd, $rs |
| Encoding: | 00110000 rd rs - - - - |
| Notes: | EVM component |

##### ADDRESS: 

| | |
|---|---|
| Description: | Sets the $rd register value to the address of the currently executing account. |
| Operation: | ADDRESS |
| Syntax: | address $rd |
| Encoding: | 00110001 rd - - - - - |
| Notes: | EVM component |

##### ORIGIN: 

| | |
|---|---|
| Description: | Sets the $rd register value to the execution origin address. This is the sender of the original transaction. Note: it is never an account with non-empty associated code. |
| Operation: | ORIGIN |
| Syntax: | origin $rd |
| Encoding: | 00110010 rd - - - - - |
| Notes: | EVM component |

##### CALLER: 

| | |
|---|---|
| Description: | Sets the $rd register value to the address of the account that is directly responsible for this execution. |
| Operation: | CALLER |
| Syntax: | caller $rd |
| Encoding: | 00110011 rd - - - - - |
| Notes: | EVM component |

##### CALLVALUE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the value deposited by the instruction/transaction responsible for this execution. |
| Operation: | CALLVALUE |
| Syntax: | callvalue $rd |
| Encoding: | 00110100 rd - - - - - |
| Notes: | EVM component |

##### CALLDATALOAD: 

| | |
|---|---|
| Description: | Takes the $rs register value, which represents the byte offset of a message call's input data, and sets the $rd register value to the 256-bit value at that offset of the calldata. |
| Operation: | CALLDATALOAD |
| Syntax: | calldataload $rd, $rs |
| Encoding: | 00110101 rd rs - - - - |
| Notes: | EVM component |

##### CALLDATASIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of a message call's input data. |
| Operation: | CALLDATASIZE |
| Syntax: | calldatasize $rd |
| Encoding: | 00110110 rd - - - - - |
| Notes: | EVM component |

##### CALLDATACOPY: 

| | |
|---|---|
| Description: | Copy calldata into memory. The $rs value represents the offset in memory to start copying values from the call data which is offset by the $rd value. The number of bytes copied is equal to the $rt value. |
| Operation: | CALLDATACOPY |
| Syntax: | calldatacopy $rd, $rs, $rt |
| Encoding: | 00110111 rd rs rt - - - |
| Notes: | EVM component |

##### CODESIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of the code being executed. |
| Operation: | CODESIZE |
| Syntax: | codesize $rd |
| Encoding: | 00111000 rd - - - - - |
| Notes: | EVM component |

##### CODECOPY: 

| | |
|---|---|
| Description: | Copy code currently being executed into memory. The $rs value represents the offset in memory to start copying values from the code data which is $rd by the second value. The number of bytes copied is equal to the $rt value. |
| Operation: | CODECOPY |
| Syntax: | codecopy $rd, $rs, $rt |
| Encoding: | 00111001 rd rs rt - - - |
| Notes: | EVM component |

##### GASPRICE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the gas price that was set in the originating transaction. |
| Operation: | GASPRICE |
| Syntax: | gasprice $rd |
| Encoding: | 00111010 rd - - - - - |
| Notes: | EVM component |

##### EXTCODESIZE: 

| | |
|---|---|
| Description: | Takes the $rs register value, an account address, and sets the $rd register value to the size of the code at that address. |
| Operation: | EXTCODESIZE |
| Syntax: | extcodesize $rd, $rs |
| Encoding: | 00111011 rd rs - - - - |
| Notes: | EVM component |

##### EXTCODECOPY: 

| | |
|---|---|
| Description: | Like CODECOPY, but the $ru parameter for this operation is the account to copy the code of. |
| Operation: | EXTCODECOPY |
| Syntax: | extcodecopy $rd, $ru, $rs, $rt |
| Encoding: | 00111100 rd rs rt ru - - |
| Notes: | EVM component |

##### RETURNDATASIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of the returned data from the last call in the current execution. |
| Operation: | RETURNDATASIZE |
| Syntax: | returndatasize $rd |
| Encoding: | 00111101 rd - - - - - |
| Notes: | EVM component |

##### RETURNDATACOPY: 

| | |
|---|---|
| Description: | Copies the return data into memory. $rs value specifies the address offset of the return data, the $rd value specifies the offset in memory to copy the data to, and the $rt value gives the number of bytes to copy. |
| Operation: | RETURNDATACOPY |
| Syntax: | returndatacopy $rd, $rs, $rt |
| Encoding: | 00111110 rd rs rt - - - |
| Notes: | EVM component |

##### EXTCODEHASH: 

| | |
|---|---|
| Description: | Takes $rs register value, the address, and sets $rd register value to the hash of the code at that address, else set it to 0. |
| Operation: | EXTCODEHASH |
| Syntax: | extcodehash $rd, $rs |
| Encoding: | 00111111 rd rs - - - - |
| Notes: | EVM component |


##### BLOCKHASH: 

| | |
|---|---|
| Description: | Takes value in register $rs, which must be a number less than 256, and sets the $rd register value to the hash of the block that is $rs blocks behind current HEAD. |
| Operation: | BLOCKHASH |
| Syntax: | blockhash $rd, $rs |
| Encoding: | 01000000 rd rs - - - - |
| Notes: | EVM component |

##### COINBASE: 

| | |
|---|---|
| Description: | Sets $rd register value to the beneficiary address (as set by the client). |
| Operation: | COINBASE |
| Syntax: | coinbase $rd |
| Encoding: | 01000001 rd - - - - - |
| Notes: | EVM component |

##### TIMESTAMP: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's timestamp (as set by the client). |
| Operation: | TIMESTAMP |
| Syntax: | timestamp $rd |
| Encoding: | 01000010 rd - - - - - |
| Notes: | EVM component |

##### NUMBER: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's number (as set by the client). |
| Operation: | NUMBER |
| Syntax: | number $rd |
| Encoding: | 01000011 rd - - - - - |
| Notes: | EVM component |

##### DIFFICULTY: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's difficulty (as set by the client). |
| Operation: | DIFFICULTY |
| Syntax: | difficulty $rd |
| Encoding: | 01000100 rd - - - - - |
| Notes: | EVM component |

##### GASLIMIT: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's gas limit (as set by the client). |
| Operation: | GASLIMIT |
| Syntax: | gaslimit $rd |
| Encoding: | 01000101 rd - - - - - |
| Notes: | EVM component |


##### LOG0: 

| | |
|---|---|
| Description: | Takes $rs and $rt register values and records a log entry with no topics based on the memory offset of the $rs value, with a length given by the $rt value. |
| Operation: | LOG0 |
| Syntax: | log0 $rs, $rt |
| Encoding: | 01000110 rs rt - - - - |
| Notes: | EVM component |

##### LOG1: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru value.  |
| Operation: | LOG1 |
| Syntax: | log1 $rs, $rt, $ru |
| Encoding: | 01000111 rs rt ru - - - |
| Notes: | EVM component |

##### LOG2: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv values.  |
| Operation: | LOG2 |
| Syntax: | log2 $rs, $rt, $ru, $rv |
| Encoding: | 01001000 rs rt ru rv - - |
| Notes: | EVM component |

##### LOG3: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw values.  |
| Operation: | LOG3 |
| Syntax: | log3 $rs, $rt, $ru, $rv, $rw |
| Encoding: | 01001001 rs rt ru rv rw - |
| Notes: | EVM component |

##### LOG4: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw, $rx values.  |
| Operation: | LOG4 |
| Syntax: | log4 $rs, $rt, $ru, $rv, $rw, $rx |
| Encoding: | 01001010 rs rt ru rv rw rx |
| Notes: | EVM component |


##### CREATE: 

| | |
|---|---|
| Description: | Creates a new account with associated code. The value of a transfer is given as the $rs register value, the input offset in memory for the associated code as the $rt register value, and the input size as the $ru value. The new account's address is stored in $rd. |
| Operation: | CREATE |
| Syntax: | create $rd, $rs, $rt, $ru |
| Encoding: | 01001011 rd rs rt ru - - |
| Notes: | EVM component |

##### CALL: 

| | |
|---|---|
| Description: | Message call into an account where the register values are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the 8th register's value if the call completes successfully. |
| Operation: | CALL |
| Syntax: | call $rd, $rs, $rt, $ru, $rv, $rw, $rx, $ry |
| Encoding: | 01001100 ** rd rs rt ru rv rw rx ry |
| Notes: | EVM component |

##### CALLCODE: 

| | |
|---|---|
| Description: | Message call into an account with the current account's code where the register values are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the 8th register's value if the call completes successfully. |
| Operation: | CALLCODE |
| Syntax: | callcode $rd, $rs, $rt, $ru, $rv, $rw, $rx, $ry |
| Encoding: | 01001101 ** rd rs rt ru rv rw rx ry |
| Notes: | EVM component |

##### RETURN: 

| | |
|---|---|
| Description: | Returns values from memory using first register value as memory offset whose size is given by the second register value. Program execution then halts. |
| Operation: | RETURN |
| Syntax: | return $rs, $rt |
| Encoding: | 01001110 rs rt - - - - |
| Notes: | EVM component |

##### DELEGATECALL: 

| | |
|---|---|
| Description: | Message-call into this account with an alternative account's code, but persisting the current values for sender and value. |
| Operation: | DELEGATECALL |
| Syntax: | delegatecall $rd, $rs, $rt, $ru, $rv, $rw, $rx |
| Encoding: | 01001111 ** rd rs rt ru rv rw rx |
| Notes: | EVM component |

##### CREATE2: 

| | |
|---|---|
| Description: | Create a new account, like CREATE, but use the salt of the fourth register value. |
| Operation: | CREATE2 |
| Syntax: | create2 $rd, $rs, $rt, $ru, $rv |
| Encoding: | 01010000 rd rs rt ru rv - |
| Notes: | EVM component |

##### STATICCALL: 

| | |
|---|---|
| Description: | Static message-call into an account. |
| Operation: | STATICCALL |
| Syntax: | staticcall $rd, $rs, $rt, $ru, $rv, $rw, $rx |
| Encoding: | 01010001 ** rd rs rt ru rv rw rx |
| Notes: | EVM component |

##### REVERT: 

| | |
|---|---|
| Description: | Halt execution reverting state changes but returning data and remaining gas. |
| Operation: | REVERT |
| Syntax: | revert $rs, $rt |
| Encoding: | 01010010 rs rt - - - - |
| Notes: | EVM component |

##### INVALID: 

| | |
|---|---|
| Description: | Designated invalid instruction. |
| Operation: | INVALID |
| Syntax: | NA |
| Encoding: | 01010011 - - - - - - |
| Notes: | EVM component |

##### SELFDESTRUCT: 

| | |
|---|---|
| Description: | Halt execution and schedule account at first register value for deletion. |
| Operation: | SELFDESTRUCT |
| Syntax: | selfdestruct $rs |
| Encoding: | 01010100 rs - - - - - |
| Notes: | EVM component |


##### POP: pop

| | |
|---|---|
| Description: | Pops a value from the stack. |
| Operation: | POP |
| Syntax: | pop $rd |
| Encoding: | 01010101 rd - - - - - |
| Notes: | Stack component |

##### PUSHX: push

| | |
|---|---|
| Description: | Pushes a X-byte value on to the stack, where X is between 1 and 32 inclusive, e.g., PUSH1 pushes a 1-byte value onto the stack. The byte values are obtained from the bytes following the current instruction in the program's code byte array. |
| Operation: | PUSHX |
| Syntax: | pushx $rs, .., $rX |
| Encoding: | 01010110 ** |
| Notes: | Stack component |

##### DUPX: duplicate

| | |
|---|---|
| Description: | Sets the X-byte value of the 1st stack item, where X is between 1 and 16 inclusive, e.g., PUSH1 pushes a 1-byte value onto the stack. Byte values are obtained from the bytes following the current instruction in the program's code byte array. Note that we opt to define the input as 1 and the output as one since the stack is not pushed. The Yellow Paper defines the number of input parameters and output parameters as X and X+1, respectively; the semantics of this interpretation is defined by the size of memory vs the number of changes to the stack. |
| Operation: | DUPX |
| Syntax: | dupx $rs |
| Encoding: | 01010111 ** |
| Notes: | Stack component |

##### SWAPX: swap

| | |
|---|---|
| Description: | Exchanges the 1st and Xth values on the stack, where X is between 1 and 16 inclusive, e.g., PUSH1 pushes 1 value onto the stack. The values pushes are obtained from the bytes following the current instruction in the program's code byte array. Note that we define the numbers of input and output parameters as based on the number of changes to the stack (instead of by the size of memory). |
| Operation: | SWAPX |
| Syntax: | swap $rd, $rs, $rt, $ru |
| Encoding: | 01011000 ** |
| Notes: | Stack component |


##### KECCAK: keccak-256

| | |
|---|---|
| Description: | Keccak-256 hash. Takes $rs and $rt register values, values which represent the range of at least one item in memory to compute the Keccak-256 hash of. The $rs value is inclusive and the $rt value is exclusive; this means that the $rt value must be greater than the $rs value. The $rd register value is set to the hash value. Note: that this opcode mnemonic differs from the SHA3 opcode as specified in the yellow paper because SHA3 is not the same as Keccak. We do this to allow proper SHA3 operations in the Fuel VM.  |
| Operation: | KECCAK |
| Syntax: | keccak $rd, $rs, $rt |
| Encoding: | 01011001 rd rs rt - - - |
| Notes: |  |


##### MOD: mod

| | |
|---|---|
| Description: | Modulo remainder of $rs by $rt and sets $rd to result. |
| Operation: | MOD |
| Syntax: | mod $rd, $rs, $rt |
| Encoding: | 01011010 rd rs rt - - - |
| Notes: | Special EVM op |

##### SMOD: mod signed

| | |
|---|---|
| Description: | Signed modulo remainder of $rs by $rt and sets $rd to result. |
| Operation: | SMOD |
| Syntax: | smod $rd, $rs, $rt |
| Encoding: | 01011011 rd rs rt - - - |
| Notes: | Special EVM op |

##### ADDMOD: add then mod

| | |
|---|---|
| Description: | Takes $rs and $rt register values, adds them, then takes the modulo of that result using the $ru register value. Sets the $rd register value to the result. |
| Operation: | ADDMOD |
| Syntax: | addmod $rd, $rs, $rt, $ru |
| Encoding: | 01011100 rd rs rt ru - - |
| Notes: | Special EVM op |

##### MULMOD: multiply then mod

| | |
|---|---|
| Description: | Takes $rs and $rt register values, multiplies them, then takes the modula of that result using $ru. Sets $rd register value to the result. |
| Operation: | MULMOD |
| Syntax: | mulmod $rd, $rs, $rt, $ru |
| Encoding: | 01011101 rd rs rt ru - - |
| Notes: | Special EVM op |

##### EXP: exponentiate

| | |
|---|---|
| Description: | Takes $rs and $rt register values, then sets $rd register value to the result of $rs taken to the power of $rt. |
| Operation: | EXP |
| Syntax: | exp $rd, $rs, $rt |
| Encoding: | 01011110 rd rs rt - - - |
| Notes: | Special EVM op |



##### MODI: mod immediate

| | |
|---|---|
| Description: | Modulo remainder of $rs using immediate value and places result in $rd. |
| Operation: | MODI |
| Syntax: | modi $rd, $rs, imm |
| Encoding: | 01011111 rd rs i i i i |
| Notes: | Special EVM op |

##### SMODI: mod signed immediate

| | |
|---|---|
| Description: | Signed modulo remainder of $rs using immediate value and places result in $rd. |
| Operation: | SMODI |
| Syntax: | smodi $rd, $rs, imm |
| Encoding: | 01100000 rd rs i i i i |
| Notes: | Special EVM op |

##### EXPI: exponentiate immediate

| | |
|---|---|
| Description: | Takes $rs value, then sets $rd register value to the result of $rs taken to the power of imm. |
| Operation: | EXPI |
| Syntax: | expi $rd, $rs, imm |
| Encoding: | 01100001 rd rt i i i i |
| Notes: | Special EVM op |




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
