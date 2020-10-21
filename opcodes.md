
# Fuel VM Opcodes

## Table of Contents

* Opcodes
  * [`STOP`](#op-STOP)
  * [`ADD`](#op-ADD)
  * [`ADDI`](#op-ADDI)
  * [`ADDIU`](#op-ADDIU)
  * [`ADDU`](#op-ADDU)
  * [`AND`](#op-AND)
  * [`ANDI`](#op-ANDI)
  * [`BEQ`](#op-BEQ)
  * [`BGEZ`](#op-BGEZ)
  * [`BGEZAL`](#op-BGEZAL)
  * [`BGTZ`](#op-BGTZ)
  * [`BLEZ`](#op-BLEZ)
  * [`BLTZ`](#op-BLTZ)
  * [`BLTZAL`](#op-BLTZAL)
  * [`BNE`](#op-BNE)
  * [`DIV`](#op-DIV)
  * [`DIVU`](#op-DIVU)
  * [`J`](#op-J)
  * [`JAL`](#op-JAL)
  * [`JR`](#op-JR)
  * [`LB`](#op-LB)
  * [`LUI`](#op-LUI)
  * [`LW`](#op-LW)
  * [`MFHI`](#op-MFHI)
  * [`MFLO`](#op-MFLO)
  * [`MULT`](#op-MULT)
  * [`MULTU`](#op-MULTU)
  * [`NOOP`](#op-NOOP)
  * [`OR`](#op-OR)
  * [`ORI`](#op-ORI)
  * [`SB`](#op-SB)
  * [`SLL`](#op-SLL)
  * [`SLLV`](#op-SLLV)
  * [`SLT`](#op-SLT)
  * [`SLTI`](#op-SLTI)
  * [`SLTIU`](#op-SLTIU)
  * [`SLTU`](#op-SLTU)
  * [`SRA`](#op-SRA)
  * [`SRL`](#op-SRL)
  * [`SRLV`](#op-SRLV)
  * [`SRAV`](#op-SRAV)
  * [`SUB`](#op-SUB)
  * [`SUBU`](#op-SUBU)
  * [`SW`](#op-SW)
  * [`SYSCALL`](#op-SYSCALL)
  * [`XOR`](#op-XOR)
  * [`XORI`](#op-XORI)
  * [`MOD`](#op-MOD)
  * [`SMOD`](#op-SMOD)
  * [`ADDMOD`](#op-ADDMOD)
  * [`MULMOD`](#op-MULMOD)
  * [`EXP`](#op-EXP)
  * [`MODI`](#op-MODI)
  * [`SMODI`](#op-SMODI)
  * [`EXPI`](#op-EXPI)
  * [`GAS`](#op-GAS)
  * [`BALANCE`](#op-BALANCE)
  * [`ADDRESS`](#op-ADDRESS)
  * [`ORIGIN`](#op-ORIGIN)
  * [`CALLER`](#op-CALLER)
  * [`CALLVALUE`](#op-CALLVALUE)
  * [`CALLDATALOAD`](#op-CALLDATALOAD)
  * [`CALLDATASIZE`](#op-CALLDATASIZE)
  * [`CALLDATACOPY`](#op-CALLDATACOPY)
  * [`CODESIZE`](#op-CODESIZE)
  * [`CODECOPY`](#op-CODECOPY)
  * [`GASPRICE`](#op-GASPRICE)
  * [`EXTCODESIZE`](#op-EXTCODESIZE)
  * [`EXTCODECOPY`](#op-EXTCODECOPY)
  * [`RETURNDATASIZE`](#op-RETURNDATASIZE)
  * [`RETURNDATACOPY`](#op-RETURNDATACOPY)
  * [`EXTCODEHASH`](#op-EXTCODEHASH)
  * [`BLOCKHASH`](#op-BLOCKHASH)
  * [`COINBASE`](#op-COINBASE)
  * [`TIMESTAMP`](#op-TIMESTAMP)
  * [`NUMBER`](#op-NUMBER)
  * [`DIFFICULTY`](#op-DIFFICULTY)
  * [`GASLIMIT`](#op-GASLIMIT)
  * [`LOG0`](#op-LOG0)
  * [`LOG1`](#op-LOG1)
  * [`LOG2`](#op-LOG2)
  * [`LOG3`](#op-LOG3)
  * [`LOG4`](#op-LOG4)
  * [`CALL`](#op-CALL)
  * [`CALLCODE`](#op-CALLCODE)
  * [`RETURN`](#op-RETURN)
  * [`DELEGATECALL`](#op-DELEGATECALL)
  * [`CREATE2`](#op-CREATE2)
  * [`STATICCALL`](#op-STATICCALL)
  * [`REVERT`](#op-REVERT)
  * [`KECCAK`](#op-KECCAK)


##### <a name='op-STOP'/>STOP: Stop

| | |
|---|---|
| Description: | Halts execution of the VM |
| Operation: | `$state = STOPPED;` |
| Syntax: | `stop` |
| Encoding: | `00000000 - - - - - -` |
| Notes: |  |

##### <a name='op-ADD'/>ADD: Add

| | |
|---|---|
| Description: | Adds two registers and stores the result in a register. |
| Operation: | `$rd = $rs + $rt; advance_pc (4);` |
| Syntax: | `add $rd, $rs, $rt` |
| Encoding: | `00000001 rd rs rt - - -` |
| Notes: | w overflow |

##### <a name='op-ADDI'/>ADDI: Add immediate

| | |
|---|---|
| Description: | Adds a register and a sign-extended immediate value and stores the result in a register. |
| Operation: | `$rt = $rs + imm; advance_pc (4);` |
| Syntax: | `addi $rd, $rs, immediate` |
| Encoding: | `00000010 rs rt i i i i` |
| Notes: | w overflow |

##### <a name='op-ADDIU'/>ADDIU: Add immediate unsigned

| | |
|---|---|
| Description: | Adds a register and a sign-extended immediate value and stores the result in a register. |
| Operation: | `$rt = $rs + imm; advance_pc (4);` |
| Syntax: | `addiu $rd, $rs, immediate` |
| Encoding: | `00000011 rs rt i i i i` |
| Notes: | no overflow |

##### <a name='op-ADDU'/>ADDU: Add variable unsigned

| | |
|---|---|
| Description: | Adds two registers and stores the result in a register. |
| Operation: | `$rd = $rs + $rt; advance_pc (4);` |
| Syntax: | `add $rd, $rs, $rt` |
| Encoding: | `00000100 rd rs rt - - -` |
| Notes: | no overflow |

##### <a name='op-AND'/>AND: And

| | |
|---|---|
| Description: | Bitwise ands two registers and stores the result in a register. |
| Operation: | `$rd = $rs & $rt; advance_pc (4);` |
| Syntax: | `and $rd, $rs, $rt` |
| Encoding: | `00000101 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-ANDI'/>ANDI: And immediate

| | |
|---|---|
| Description: | Bitwise ands a register and an immediate value and stores the result in a register. |
| Operation: | `$rd = $rs & imm; advance_pc (4);` |
| Syntax: | `andi $rd, $rs, imm` |
| Encoding: | `00000110 rd rs - - - -` |
| Notes: |  |

##### <a name='op-BEQ'/>BEQ: Branch on equal

| | |
|---|---|
| Description: | Branches if the two registers are equal  |
| Operation: | `if $rs == $rt advance_pc (offset << 2)); else advance_pc (4); ` |
| Syntax: | `beq $rs, $rt, offset ` |
| Encoding: | `00000111 rs rt i i i i` |
| Notes: |  |

##### <a name='op-BGEZ'/>BGEZ: Branch on greater than or equal to zero

| | |
|---|---|
| Description: | Branches if the register is greater than or equal to zero  |
| Operation: | `if $rs >= 0 advance_pc (offset << 2)); else advance_pc (4); ` |
| Syntax: | `bgez $rs, offset ` |
| Encoding: | `00001000 rs - i i i i` |
| Notes: |  |

##### <a name='op-BGEZAL'/>BGEZAL: Branch on greater than or equal to zero and link

| | |
|---|---|
| Description: | Branches if the register is greater than or equal to zero and saves the return address in $31  |
| Operation: | `if $rs >= 0 $31 = PC + 8 (or nPC + 4); advance_pc (offset << 2)); else advance_pc (4);` |
| Syntax: | `bgezal $rs, offset ` |
| Encoding: | `00001001 rs - i i i i` |
| Notes: |  |

##### <a name='op-BGTZ'/>BGTZ: Branch on greater than zero

| | |
|---|---|
| Description: | Branches if the register is greater than zero  |
| Operation: | `if $rs > 0 advance_pc (offset << 2)); else advance_pc (4);` |
| Syntax: | `bgtz $rs, offset ` |
| Encoding: | `00001010 rs - i i i i` |
| Notes: |  |

##### <a name='op-BLEZ'/>BLEZ: Branch on less than on equal to zero

| | |
|---|---|
| Description: | Branches if the register is less than or equal to zero  |
| Operation: | `if $rs <= 0 advance_pc (offset << 2)); else advance_pc (4); ` |
| Syntax: | `blez $rs, offset ` |
| Encoding: | `00001011 rs - i i i i` |
| Notes: |  |

##### <a name='op-BLTZ'/>BLTZ: Branch on less than zero

| | |
|---|---|
| Description: | Branches if the register is less than zero  |
| Operation: | `if $rs < 0 advance_pc (offset << 2)); else advance_pc (4); ` |
| Syntax: | `bltz $rs, offset ` |
| Encoding: | `00001100 rs - i i i i` |
| Notes: |  |

##### <a name='op-BLTZAL'/>BLTZAL: Branch on less than zero and link

| | |
|---|---|
| Description: | Branches if the register is less than zero and saves the return address in $31 |
| Operation: | `if $rs < 0 $31 = PC + 8 (or nPC + 4); advance_pc (offset << 2)); else advance_pc (4); ` |
| Syntax: | `bltzal $rs, offset ` |
| Encoding: | `00001101 rs - i i i i` |
| Notes: |  |

##### <a name='op-BNE'/>BNE: Branch on not equal

| | |
|---|---|
| Description: | Branches if the two registers are not equal |
| Operation: | `if $rs != $rt advance_pc (offset << 2)); else advance_pc (4); ` |
| Syntax: | `bne $rs, $rt, offset ` |
| Encoding: | `00001110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-DIV'/>DIV: Divide

| | |
|---|---|
| Description: | Divides $rs by $rt and stores the quotient in $lo and the remainder in $hi  |
| Operation: | `$lo = $rs / $rt; $hi = $rs % $rt; advance_pc (4);` |
| Syntax: | `div $rs, $rt` |
| Encoding: | `00001111 rs rt - - - -` |
| Notes: |  |

##### <a name='op-DIVU'/>DIVU: Divide unsigned

| | |
|---|---|
| Description: | Divides $rs by $rt and stores the quotient in $lo and the remainder in $hi  |
| Operation: | `$lo = $rs / $rt; $hi = $rs % $rt; advance_pc (4); ` |
| Syntax: | `divu $rs, $rt` |
| Encoding: | `00010000 rs rt - - - -` |
| Notes: |  |

##### <a name='op-J'/>J: Jump

| | |
|---|---|
| Description: | Jumps to the calculated address  |
| Operation: | `PC = nPC; nPC = (PC & 0xf0000000) | (target << 2);` |
| Syntax: | `j target` |
| Encoding: | `00010001 i i i i i i` |
| Notes: |  |

##### <a name='op-JAL'/>JAL: Jump and link

| | |
|---|---|
| Description: | Jumps to the calculated address and stores the return address in $31  |
| Operation: | `$31 = PC + 8 (or nPC + 4); PC = nPC; nPC = (PC & 0xf0000000) | (target << 2);` |
| Syntax: | `jal target` |
| Encoding: | `00010010 i i i i i i` |
| Notes: | register $31 used as link target |

##### <a name='op-JR'/>JR: Jump register

| | |
|---|---|
| Description: | Jump to the address contained in register $rs |
| Operation: | `PC = nPC; nPC = $rs; ` |
| Syntax: | `jr $rs` |
| Encoding: | `00010011 rs - - - - -` |
| Notes: |  |

##### <a name='op-LB'/>LB: Load byte

| | |
|---|---|
| Description: | A byte is loaded into a register from the specified address  |
| Operation: | `$rt = MEM[$rs + offset]; advance_pc (4);` |
| Syntax: | `lb $rt, offset($rs)` |
| Encoding: | `00010100 rs rt i i i i` |
| Notes: |  |

##### <a name='op-LUI'/>LUI: Load upper immediate

| | |
|---|---|
| Description: | The immediate value is shifted left 16 bits and stored in the register. The lower 16 bits are zeroes. |
| Operation: | `$rt = (imm << 16); advance_pc(4);` |
| Syntax: | `lui $rt, imm` |
| Encoding: | `00010101 - rt i i i i` |
| Notes: |  |

##### <a name='op-LW'/>LW: Load word

| | |
|---|---|
| Description: | A word is loaded into a register from the specified address  |
| Operation: | `$rt = MEM[$rs + offset]; advance_pc (4);` |
| Syntax: | `lw $rt, offset($rs)` |
| Encoding: | `00010110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-MFHI'/>MFHI: Move from HI to register

| | |
|---|---|
| Description: | The contents of register $hi are moved to the specified register.  |
| Operation: | `$rd = $hi; advance_pc(4);` |
| Syntax: | `mfhi $rd` |
| Encoding: | `00010111 rd - - - - -` |
| Notes: |  |

##### <a name='op-MFLO'/>MFLO: Move from LO to register

| | |
|---|---|
| Description: | The contents of register $lo are moved to the specified register. |
| Operation: | `$rd = $lo; advance_pc(4);` |
| Syntax: | `mflo $rd` |
| Encoding: | `00011000 rd - - - - -` |
| Notes: |  |

##### <a name='op-MULT'/>MULT: Multiply signed

| | |
|---|---|
| Description: | Multiplies $rs by $rt and stores the result in $lo.  |
| Operation: | `$rd = $rs * $rt; advance_pc (4);` |
| Syntax: | `mult $rd, $rs, $rt` |
| Encoding: | `00011001 rd rs rt - - -` |
| Notes: | with overflow |

##### <a name='op-MULTU'/>MULTU: Multiply unsigned

| | |
|---|---|
| Description: | Multiplies $rs by $rt and stores the result in $lo.  |
| Operation: | `$rd = $rs * $rt; advance_pc (4);` |
| Syntax: | `mult $rd, $rs, $rt` |
| Encoding: | `00011010 rd rs rt - - -` |
| Notes: | no overflow |

##### <a name='op-NOOP'/>NOOP: No operation

| | |
|---|---|
| Description: | Performs no operation.  |
| Operation: | `advance_pc (4);` |
| Syntax: | `noop` |
| Encoding: | `00011011 - - - - -` |
| Notes: |  |

##### <a name='op-OR'/>OR: Or

| | |
|---|---|
| Description: | Bitwise logical ors registers given by $rs and $rt and stores the result in $rd register. |
| Operation: | `$rd = $rs | $rt; advance_pc(4);` |
| Syntax: | `or $rd, $rs, $rt` |
| Encoding: | `00011100 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-ORI'/>ORI: Or immediate

| | |
|---|---|
| Description: | Bitwise ors register $rs and an immediate value and stores the result in $rd register |
| Operation: | `$rd = $rs | imm; advance_pc(4);` |
| Syntax: | `ori $rd, $rs, imm` |
| Encoding: | `00011101 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SB'/>SB: Store byte

| | |
|---|---|
| Description: | The least significant byte of $rt is stored at the specified address.  |
| Operation: | `MEM[$rs + offset] = (0xff & $rt); advance_pc (4);` |
| Syntax: | `sb $rt, offset($rs)` |
| Encoding: | `00011110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-SLL'/>SLL: Shift left logical

| | |
|---|---|
| Description: | Shifts $rs register value left by the shift amount imm and places the result in $rd register. Zeroes are shifted in. |
| Operation: | `$rd = $rs << imm; advance_pc (4); ` |
| Syntax: | `sll $rd, $rs, imm` |
| Encoding: | `00011111 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SLLV'/>SLLV: Shift left logical variable

| | |
|---|---|
| Description: | Shifts $rs register value left by the value in $rt register and places the result in $rd. Zeroes are shifted in.  |
| Operation: | `$rd = $rs << $rt; advance_pc (4); ` |
| Syntax: | `sllv $rd, $rs, $rt` |
| Encoding: | `00100000 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SLT'/>SLT: Set if less than signed

| | |
|---|---|
| Description: | If $rs is less than $rt, $rd is set to one. It gets zero otherwise.  |
| Operation: | `if $rs < $rt { $rd = 1; } else { $rd = 0; } advance_pc (4);` |
| Syntax: | `slt $rd, $rs, $rt` |
| Encoding: | `00100001 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SLTI'/>SLTI: Set if less than immediate signed

| | |
|---|---|
| Description: | If $rs is less than immediate, $rt is set to one. It gets zero otherwise.  |
| Operation: | `if $rs < imm { $rt = 1; } else { $rt = 0; } advance_pc (4); ` |
| Syntax: | `slti $rs, $rt, imm` |
| Encoding: | `00100010 rs rt i i i i` |
| Notes: |  |

##### <a name='op-SLTIU'/>SLTIU: Set if less than immediate unsigned

| | |
|---|---|
| Description: | If $rs is less than the unsigned immediate, $rt is set to one. It gets zero otherwise.  |
| Operation: | `if $rs < imm { $rt = 1; } else { $rt = 0; } advance_pc (4);` |
| Syntax: | `sltiu $rs, $rt, imm` |
| Encoding: | `00100011 rs rt i i i i` |
| Notes: |  |

##### <a name='op-SLTU'/>SLTU: Set if less than unsigned

| | |
|---|---|
| Description: | If $rs is less than $rt, $rd is set to one. It gets zero otherwise.  |
| Operation: | `if $rs < $rt { $rd = 1; } else { $rd = 0; } advance_pc (4);` |
| Syntax: | `sltu $rd, $rs, $rt` |
| Encoding: | `00100100 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SRA'/>SRA: Shift right arithmetic

| | |
|---|---|
| Description: | Shifts a register value right by the shift amount (imm) and places the value in the destination register. The sign bit is shifted in.  |
| Operation: | `$rd = $rs >> imm; advance_pc(4);` |
| Syntax: | `sra $rd, $rs, imm` |
| Encoding: | `00100101 rd rs i i i i` |
| Notes: | signed, sign bit is shifted in. MIPS uses shamt field in instruction instead of imm. |

##### <a name='op-SRL'/>SRL: Shift right logical

| | |
|---|---|
| Description: | Shifts a register value right by the shift amount (imm) and places the value in the destination register. Zeroes are shifted in.  |
| Operation: | `$rd = $rs >> imm; advance_pc(4);` |
| Syntax: | `srl $rd, $rs, imm` |
| Encoding: | `00100110 rd rs i i i i` |
| Notes: | unsigned, zero is shifted in. MIPS uses shamt field in instruction instead of imm. |

##### <a name='op-SRLV'/>SRLV: Shift right logical variable

| | |
|---|---|
| Description: | Shifts a register value right by the amount specified in $rs and places the value in the destination register. Zeroes are shifted in.  |
| Operation: | `$rd = $rs >> $rt; advance_pc(4);` |
| Syntax: | `srlv $rd, $rs, $rt` |
| Encoding: | `00100111 rd rs rt - - -` |
| Notes: | unsigned, zero is shifted in |

##### <a name='op-SRAV'/>SRAV: Shift right arithmetic variable

| | |
|---|---|
| Description: | Shifts a register value right by the amount specified in $rs and places the value in the destination register. The sign bit is shifted in.  |
| Operation: | `$rd = $rs >> $rt; advance_pc(4);` |
| Syntax: | `srav $rd, $rs, $rt` |
| Encoding: | `00101000 rd rs rt - - -` |
| Notes: | signed, sign bit is shifted in. Not part of MIPS instructions. |

##### <a name='op-SUB'/>SUB: Subtract

| | |
|---|---|
| Description: | Subtracts two registers and stores the result in a register |
| Operation: | `$rd = $rs - $rt; advance_pc(4);` |
| Syntax: | `sub $rd, $rs, $rt` |
| Encoding: | `00101001 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SUBU'/>SUBU: Subtract unsigned

| | |
|---|---|
| Description: | Subtracts two registers and stores the result in a register  |
| Operation: | `$rd = $rs - $rt; advance_pc(4);` |
| Syntax: | `subu $rd, $rs, $rt` |
| Encoding: | `00101010 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SW'/>SW: Store word

| | |
|---|---|
| Description: | The contents of $rt is stored at the specified address.  |
| Operation: | `MEM[$rs + offset] = $rt; advance_pc(4);` |
| Syntax: | `sw $rd, offset($rs)` |
| Encoding: | `00101011 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SYSCALL'/>SYSCALL: System call

| | |
|---|---|
| Description: | nop for the time being. |
| Operation: | `nop; advance_pc(4);` |
| Syntax: | `syscall` |
| Encoding: | `00101100 ` |
| Notes: |  |

##### <a name='op-XOR'/>XOR: Xor

| | |
|---|---|
| Description: | Exclusive ors two registers and stores the result in a register. |
| Operation: | `$rd = $rs ^ $rt; advance_pc(4);` |
| Syntax: | `xor $rd, $rs, $rt ` |
| Encoding: | `00101101 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-XORI'/>XORI: Xor immediate

| | |
|---|---|
| Description: | Bitwise exclusive ors a register and an immediate value and stores the result in a register. |
| Operation: | `$rd = $rs ^ imm; advance_pc(4);` |
| Syntax: | `xori $rt, $rs, imm ` |
| Encoding: | `00101110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-MOD'/>MOD: Mod

| | |
|---|---|
| Description: | Modulo remainder of $rs by $rt and sets $rd to result. |
| Operation: | `div $rs, $rt; mfhi $rd; advance_pc(4);` |
| Syntax: | `mod $rd, $rs, $rt` |
| Encoding: | `00101111 rd rs rt - - -` |
| Notes: | Special EVM op |

##### <a name='op-SMOD'/>SMOD: Mod signed

| | |
|---|---|
| Description: | Signed modulo remainder of $rs by $rt and sets $rd to result. |
| Operation: | `sdiv $rs, $rt; mfhi $rd; advance_pc(4);` |
| Syntax: | `smod $rd, $rs, $rt` |
| Encoding: | `00110000 rd rs rt - - -` |
| Notes: | Special EVM op |

##### <a name='op-ADDMOD'/>ADDMOD: Add then mod

| | |
|---|---|
| Description: | Takes $rs and $rt register values, adds them, then takes the modulo of that result using the $ru register value. Sets the $rd register value to the result. |
| Operation: | `add $rd, $rs, $rt; div $rd, $ru; mfhi $rd; advance_pc(4);` |
| Syntax: | `addmod $rd, $rs, $rt, $ru` |
| Encoding: | `00110001 rd rs rt ru - -` |
| Notes: | Special EVM op |

##### <a name='op-MULMOD'/>MULMOD: Multiply then mod

| | |
|---|---|
| Description: | Takes $rs and $rt register values, multiplies them, then takes the modula of that result using $ru. Sets $rd register value to the result. |
| Operation: | `mul $rd, $rs, $rt; div $rd, $ru; mfhi $rd; advance_pc(4);` |
| Syntax: | `mulmod $rd, $rs, $rt, $ru` |
| Encoding: | `00110010 rd rs rt ru - -` |
| Notes: | Special EVM op |

##### <a name='op-EXP'/>EXP: Exponentiate

| | |
|---|---|
| Description: | Takes $rs and $rt register values, then sets $rd register value to the result of $rs taken to the power of $rt. |
| Operation: | `for (int i = 0; i < $rt) { mul $rd, $rd, $rs; }; advance_pc(4);` |
| Syntax: | `exp $rd, $rs, $rt` |
| Encoding: | `00110011 rd rs rt - - -` |
| Notes: | Special EVM op |

##### <a name='op-MODI'/>MODI: Mod immediate

| | |
|---|---|
| Description: | Modulo remainder of $rs using immediate value and places result in $rd. |
| Operation: | `div $rs, imm; mfhi $rd; advance_pc(4);` |
| Syntax: | `modi $rd, $rs, imm` |
| Encoding: | `00110100 rd rs i i i i` |
| Notes: | Special EVM op |

##### <a name='op-SMODI'/>SMODI: Mod signed immediate

| | |
|---|---|
| Description: | Signed modulo remainder of $rs using immediate value and places result in $rd. |
| Operation: | `sdiv $rs, imm; mfhi $rd; advance_pc(4);` |
| Syntax: | `smodi $rd, $rs, imm` |
| Encoding: | `00110101 rd rs i i i i` |
| Notes: | Special EVM op |

##### <a name='op-EXPI'/>EXPI: Exponentiate immediate

| | |
|---|---|
| Description: | Takes $rs value, then sets $rd register value to the result of $rs taken to the power of imm. |
| Operation: | `for (int i = 0; i < $rt) { mul $rd, $rd, imm; }; advance_pc(4);` |
| Syntax: | `expi $rd, $rs, imm` |
| Encoding: | `00110110 rd rt i i i i` |
| Notes: | Special EVM op |

##### <a name='op-GAS'/>GAS: 

| | |
|---|---|
| Description: | Sets the $rd register value to the numeric value of gas remaining. |
| Operation: | `$rd = $gas; advance_pc(4);` |
| Syntax: | `gas $rd` |
| Encoding: | `10000000 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-BALANCE'/>BALANCE: 

| | |
|---|---|
| Description: | Takes the $rs register value, which is an address, and then sets the $rd register value to the balance of given address. |
| Operation: | `$rd = $balance[$rs]; advance_pc(4);` |
| Syntax: | `balance $rd, $rs` |
| Encoding: | `10000001 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-ADDRESS'/>ADDRESS: 

| | |
|---|---|
| Description: | Sets the $rd register value to the address of the currently executing account. |
| Operation: | `$rd = $address; advance_pc(4);` |
| Syntax: | `address $rd` |
| Encoding: | `10000010 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-ORIGIN'/>ORIGIN: 

| | |
|---|---|
| Description: | Sets the $rd register value to the execution origin address. This is the sender of the original transaction. Note: it is never an account with non-empty associated code. |
| Operation: | `$rd = $origin; advance_pc(4);` |
| Syntax: | `origin $rd` |
| Encoding: | `10000011 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLER'/>CALLER: 

| | |
|---|---|
| Description: | Sets the $rd register value to the address of the account that is directly responsible for this execution. |
| Operation: | `$rd = $caller; advance_pc(4);` |
| Syntax: | `caller $rd` |
| Encoding: | `10000100 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLVALUE'/>CALLVALUE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the value deposited by the instruction/transaction responsible for this execution. |
| Operation: | `$rd = $value; advance_pc(4);` |
| Syntax: | `callvalue $rd` |
| Encoding: | `10000101 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLDATALOAD'/>CALLDATALOAD: 

| | |
|---|---|
| Description: | Takes the $rs register value, which represents the byte offset of a message call's input data, and sets the $rd register value to the 256-bit value at that offset of the calldata. |
| Operation: | `$rd = $calldata[$rs]; advance_pc(4);` |
| Syntax: | `calldataload $rd, $rs` |
| Encoding: | `10000110 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLDATASIZE'/>CALLDATASIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of a message call's input data. |
| Operation: | `$rd = size($calldata); advance_pc(4);` |
| Syntax: | `calldatasize $rd` |
| Encoding: | `10000111 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLDATACOPY'/>CALLDATACOPY: 

| | |
|---|---|
| Description: | Copy calldata into memory. The $rs value represents the offset in memory to start copying values from the call data which is offset by the $rd value. The number of bytes copied is equal to the $rt value. |
| Operation: | `MEM[$rd] = $calldata[$rs..$rs+$rt]; advance_pc(4);` |
| Syntax: | `calldatacopy $rd, $rs, $rt` |
| Encoding: | `10001000 rd rs rt - - -` |
| Notes: | EVM component |

##### <a name='op-CODESIZE'/>CODESIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of the code being executed. |
| Operation: | `$rd = $codesize($address); advance_pc(4);` |
| Syntax: | `codesize $rd` |
| Encoding: | `10001001 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CODECOPY'/>CODECOPY: 

| | |
|---|---|
| Description: | Copy code currently being executed into memory. The $rs value represents the offset in memory to start copying values from the code data which is $rd by the second value. The number of bytes copied is equal to the $rt value. |
| Operation: | `MEM[$rd] = $code[$rs..$rs+$rt]; advance_pc(4);` |
| Syntax: | `codecopy $rd, $rs, $rt` |
| Encoding: | `10001010 rd rs rt - - -` |
| Notes: | EVM component |

##### <a name='op-GASPRICE'/>GASPRICE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the gas price that was set in the originating transaction. |
| Operation: | `$rd = $gasprice; advance_pc(4);` |
| Syntax: | `gasprice $rd` |
| Encoding: | `10001011 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-EXTCODESIZE'/>EXTCODESIZE: 

| | |
|---|---|
| Description: | Takes the $rs register value, an account address, and sets the $rd register value to the size of the code at that address. |
| Operation: | `$rd = $codesize($rs); advance_pc(4);` |
| Syntax: | `extcodesize $rd, $rs` |
| Encoding: | `10001100 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-EXTCODECOPY'/>EXTCODECOPY: 

| | |
|---|---|
| Description: | Like CODECOPY, but the $ru parameter for this operation is the account to copy the code of. |
| Operation: | `MEM[$rd] = $code[$ru][$rs..$rs+$rt]; advance_pc(4);` |
| Syntax: | `extcodecopy $rd, $ru, $rs, $rt` |
| Encoding: | `10001101 rd rs rt ru - -` |
| Notes: | EVM component |

##### <a name='op-RETURNDATASIZE'/>RETURNDATASIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of the returned data from the last call in the current execution. |
| Operation: | `$rd = size($returndata); advance_pc(4);` |
| Syntax: | `returndatasize $rd` |
| Encoding: | `10001110 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-RETURNDATACOPY'/>RETURNDATACOPY: 

| | |
|---|---|
| Description: | Copies the return data into memory. $rs value specifies the address offset of the return data, the $rd value specifies the offset in memory to copy the data to, and the $rt value gives the number of bytes to copy. |
| Operation: | `MEM[$rd] = $returndata[$rs..$rs+$rt]; advance_pc(4);` |
| Syntax: | `returndatacopy $rd, $rs, $rt` |
| Encoding: | `10001111 rd rs rt - - -` |
| Notes: | EVM component |

##### <a name='op-EXTCODEHASH'/>EXTCODEHASH: 

| | |
|---|---|
| Description: | Takes $rs register value, the address, and sets $rd register value to the hash of the code at that address, else set it to 0. |
| Operation: | `$rd = $codehash[$rs]; advance_pc(4);` |
| Syntax: | `extcodehash $rd, $rs` |
| Encoding: | `10010000 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-BLOCKHASH'/>BLOCKHASH: 

| | |
|---|---|
| Description: | Takes 64-bit block number value from memory at address given by register $rs, and sets $rd register value to the hash of the block that at that block number. |
| Operation: | `$rd = $blockhash(MEM[$rs]); advance_pc(4);` |
| Syntax: | `blockhash $rd, $rs` |
| Encoding: | `10010001 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-COINBASE'/>COINBASE: 

| | |
|---|---|
| Description: | Sets $rd register value to the beneficiary address (as set by the client). |
| Operation: | `$rd = $coinbase; advance_pc(4);` |
| Syntax: | `coinbase $rd` |
| Encoding: | `10010010 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-TIMESTAMP'/>TIMESTAMP: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's timestamp (as set by the client). |
| Operation: | `$rd = $timestamp; advance_pc(4);` |
| Syntax: | `timestamp $rd` |
| Encoding: | `10010011 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-NUMBER'/>NUMBER: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's number (as set by the client). |
| Operation: | `$rd = $number; advance_pc(4);` |
| Syntax: | `number $rd` |
| Encoding: | `10010100 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-DIFFICULTY'/>DIFFICULTY: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's difficulty (as set by the client). |
| Operation: | `$rd = $difficulty; advance_pc(4);` |
| Syntax: | `difficulty $rd` |
| Encoding: | `10010101 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-GASLIMIT'/>GASLIMIT: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's gas limit (as set by the client). |
| Operation: | `$rd = $gaslimit; advance_pc(4);` |
| Syntax: | `gaslimit $rd` |
| Encoding: | `10010110 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-LOG0'/>LOG0: 

| | |
|---|---|
| Description: | Takes $rs and $rt register values and records a log entry with no topics based on the memory offset of the $rs value, with a length given by the $rt value. |
| Operation: | `$log.push("":"" + MEM[$rs..$rs+$rt]); advance_pc(4);` |
| Syntax: | `log0 $rs, $rt` |
| Encoding: | `10010111 rs rt - - - -` |
| Notes: | EVM component |

##### <a name='op-LOG1'/>LOG1: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru value.  |
| Operation: | `$log.push($ru + "":"" + MEM[$rs..$rs+$rt]); advance_pc(4);` |
| Syntax: | `log1 $rs, $rt, $ru` |
| Encoding: | `10011000 rs rt ru - - -` |
| Notes: | EVM component |

##### <a name='op-LOG2'/>LOG2: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv values.  |
| Operation: | `$log.push($ru + $rv + "":"" + MEM[$rs..$rs+$rt]); advance_pc(4);` |
| Syntax: | `log2 $rs, $rt, $ru, $rv` |
| Encoding: | `10011001 rs rt ru rv - -` |
| Notes: | EVM component |

##### <a name='op-LOG3'/>LOG3: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw values.  |
| Operation: | `$log.push($ru + $rv + $rw + "":"" + MEM[$rs..$rs+$rt]); advance_pc(4);` |
| Syntax: | `log3 $rs, $rt, $ru, $rv, $rw` |
| Encoding: | `10011010 rs rt ru rv rw -` |
| Notes: | EVM component |

##### <a name='op-LOG4'/>LOG4: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw, $rx values.  |
| Operation: | `$log.push($ru + $rv + $rw + $rx + "":"" + MEM[$rs..$rs+$rt]); advance_pc(4);` |
| Syntax: | `log4 $rs, $rt, $ru, $rv, $rw, $rx` |
| Encoding: | `10011011 rs rt ru rv rw rx` |
| Notes: | EVM component |

##### <a name='op-CALL'/>CALL: 

| | |
|---|---|
| Description: | Message call into an account where the register values are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the 8th register's value if the call completes successfully. |
| Operation: | `""pre-compile""` |
| Syntax: | `call $rd, $rs, $rt, $ru, $rv, $rw, $rx, $ry` |
| Encoding: | `10011100 ** rd rs rt ru rv rw rx ry` |
| Notes: | EVM component |

##### <a name='op-CALLCODE'/>CALLCODE: 

| | |
|---|---|
| Description: | Message call into an account with the current account's code where the register values are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the 8th register's value if the call completes successfully. |
| Operation: | `""pre-compile""` |
| Syntax: | `callcode $rd, $rs, $rt, $ru, $rv, $rw, $rx, $ry` |
| Encoding: | `10011101 ** rd rs rt ru rv rw rx ry` |
| Notes: | EVM component |

##### <a name='op-RETURN'/>RETURN: 

| | |
|---|---|
| Description: | Returns values from memory using first register value as memory offset whose size is given by the second register value. Program execution then halts. |
| Operation: | `""pre-compile""` |
| Syntax: | `return $rs, $rt` |
| Encoding: | `10011110 rs rt - - - -` |
| Notes: | EVM component |

##### <a name='op-DELEGATECALL'/>DELEGATECALL: 

| | |
|---|---|
| Description: | Message-call into this account with an alternative account's code, but persisting the current values for sender and value. |
| Operation: | `""pre-compile""` |
| Syntax: | `delegatecall $rd, $rs, $rt, $ru, $rv, $rw, $rx` |
| Encoding: | `10011111 ** rd rs rt ru rv rw rx` |
| Notes: | EVM component |

##### <a name='op-CREATE2'/>CREATE2: 

| | |
|---|---|
| Description: | Create a new account, like CREATE, but use the salt of the fourth register value. |
| Operation: | `""pre-compile""` |
| Syntax: | `create2 $rd, $rs, $rt, $ru, $rv` |
| Encoding: | `10100000 rd rs rt ru rv -` |
| Notes: | EVM component |

##### <a name='op-STATICCALL'/>STATICCALL: 

| | |
|---|---|
| Description: | Static message-call into an account. |
| Operation: | `""pre-compile""` |
| Syntax: | `staticcall $rd, $rs, $rt, $ru, $rv, $rw, $rx` |
| Encoding: | `10100001 ** rd rs rt ru rv rw rx` |
| Notes: | EVM component |

##### <a name='op-REVERT'/>REVERT: 

| | |
|---|---|
| Description: | Halt execution reverting state changes but returning data and remaining gas. |
| Operation: | `""pre-compile""` |
| Syntax: | `revert $rs, $rt` |
| Encoding: | `10100010 rs rt - - - -` |
| Notes: | EVM component |

##### <a name='op-KECCAK'/>KECCAK: keccak-256

| | |
|---|---|
| Description: | Keccak-256 hash. Takes $rs and $rt register values, values which represent the range of at least one item in memory to compute the Keccak-256 hash of. The $rs value is inclusive and the $rt value is exclusive; this means that the $rt value must be greater than the $rs value. The $rd register value is set to the hash value. Note: that this opcode mnemonic differs from the SHA3 opcode as specified in the yellow paper because SHA3 is not the same as Keccak. We do this to allow proper SHA3 operations in the Fuel VM.  |
| Operation: | `$rd = keccak(MEM[$rs:$rt]); advance_pc (4); ` |
| Syntax: | `keccak $rd, $rs, $rt` |
| Encoding: | `10100011 rd rs rt - - -` |
| Notes: |  |

