# Fuel VM Opcodes

## Table of Contents


* [Arithmetic/Logic (ALU) Operands](#op-group-id-7)
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

* [Ethereum Operands](#op-group-id-64)
  * [`GAS`](#op-GAS)
  * [`GASLIMIT`](#op-GASLIMIT)
  * [`GASPRICE`](#op-GASPRICE)
  * [`BALANCE`](#op-BALANCE)
  * [`ADDRESS`](#op-ADDRESS)
  * [`ORIGIN`](#op-ORIGIN)
  * [`CALLER`](#op-CALLER)
  * [`CALLVALUE`](#op-CALLVALUE)
  * [`BLOCKHASH`](#op-BLOCKHASH)
  * [`COINBASE`](#op-COINBASE)
  * [`TIMESTAMP`](#op-TIMESTAMP)
  * [`NUMBER`](#op-NUMBER)
  * [`DIFFICULTY`](#op-DIFFICULTY)
  * [`CALLDATASIZE`](#op-CALLDATASIZE)
  * [`CALLDATALOAD`](#op-CALLDATALOAD)
  * [`CALLDATACOPY`](#op-CALLDATACOPY)
  * [`CODESIZE`](#op-CODESIZE)
  * [`CODECOPY`](#op-CODECOPY)
  * [`EXTCODESIZE`](#op-EXTCODESIZE)
  * [`EXTCODEHASH`](#op-EXTCODEHASH)
  * [`EXTCODECOPY`](#op-EXTCODECOPY)
  * [`RETURNDATASIZE`](#op-RETURNDATASIZE)
  * [`RETURNDATACOPY`](#op-RETURNDATACOPY)
  * [`CALL`](#op-CALL)
  * [`CALLCODE`](#op-CALLCODE)
  * [`DELEGATECALL`](#op-DELEGATECALL)
  * [`STATICCALL`](#op-STATICCALL)
  * [`RETURN`](#op-RETURN)
  * [`CREATE2`](#op-CREATE2)
  * [`REVERT`](#op-REVERT)
  * [`KECCAK`](#op-KECCAK)
  * [`LOG0`](#op-LOG0)
  * [`LOG1`](#op-LOG1)
  * [`LOG2`](#op-LOG2)
  * [`LOG3`](#op-LOG3)
  * [`LOG4`](#op-LOG4)

* [EIP-615: Subroutines and Static Jumps](#op-group-id-102)
  * [`JUMPTO`](#op-JUMPTO)
  * [`JUMPIF`](#op-JUMPIF)
  * [`JUMPV`](#op-JUMPV)
  * [`JUMPSUB`](#op-JUMPSUB)
  * [`JUMPSUBV`](#op-JUMPSUBV)
  * [`RETURNSUB`](#op-RETURNSUB)
  * [`GETLOCAL`](#op-GETLOCAL)
  * [`PUTLOCAL`](#op-PUTLOCAL)
  * [`BEGINSUB`](#op-BEGINSUB)
  * [`BEGINDATA`](#op-BEGINDATA)

* [EIP-616: SIMD Operations ](#op-group-id-114)
  * [`SIMDADD`](#op-SIMDADD)
  * [`SIMDMUL`](#op-SIMDMUL)
  * [`SIMSUB`](#op-SIMSUB)
  * [`SIMDDIV`](#op-SIMDDIV)
  * [`SIMDSDIV`](#op-SIMDSDIV)
  * [`SIMDMOD`](#op-SIMDMOD)
  * [`SIMDSMOD`](#op-SIMDSMOD)
  * [`SIMDLT`](#op-SIMDLT)
  * [`SIMDGT`](#op-SIMDGT)
  * [`SIMDSLT`](#op-SIMDSLT)
  * [`SIMDSGT`](#op-SIMDSGT)
  * [`SIMDEQ`](#op-SIMDEQ)
  * [`SIMDISZERO`](#op-SIMDISZERO)
  * [`SIMDAND`](#op-SIMDAND)
  * [`SIMDOR`](#op-SIMDOR)
  * [`SIMDXOR`](#op-SIMDXOR)
  * [`SIMDNOT`](#op-SIMDNOT)
  * [`SIMDSHL`](#op-SIMDSHL)
  * [`SIMDSHR`](#op-SIMDSHR)
  * [`SIMDSAR`](#op-SIMDSAR)
  * [`SIMDROL`](#op-SIMDROL)
  * [`SIMDROR`](#op-SIMDROR)
  * [`SIMDINDEX`](#op-SIMDINDEX)
  * [`SIMDSHUFFLE`](#op-SIMDSHUFFLE)

* [MAST Proof mechanisms](#op-group-id-140)
  * [`CHECKPROOF`](#op-CHECKPROOF)

* [Covenants: Template-checking mechanisms](#op-group-id-143)
  * [`CHECKTEMPLATE`](#op-CHECKTEMPLATE)






#### <a name='op-group-id-7'/>Arithmetic/Logic (ALU) Operands

##### <a name='op-STOP'/>STOP: Stop

| | |
|---|---|
| Description: | Halts execution of the VM. |
| Operation: | ```$state = STOPPED;``` |
| Syntax: | `stop` |
| Encoding: | `00000000 - - - - - -` |
| Notes: |  |

##### <a name='op-ADD'/>ADD: Add

| | |
|---|---|
| Description: | Adds two registers and stores the result in a register. |
| Operation: | ```$rd = $rs + $rt;```<br>```advance_pc();``` |
| Syntax: | `add $rd, $rs, $rt` |
| Encoding: | `00000001 rd rs rt - - -` |
| Notes: | w overflow |

##### <a name='op-ADDI'/>ADDI: Add immediate

| | |
|---|---|
| Description: | Adds a register and a sign-extended immediate value and stores the result in a register. |
| Operation: | ```$rt = $rs + imm;```<br>```advance_pc();``` |
| Syntax: | `addi $rd, $rs, immediate` |
| Encoding: | `00000010 rs rt i i i i` |
| Notes: | w overflow |

##### <a name='op-ADDIU'/>ADDIU: Add immediate unsigned

| | |
|---|---|
| Description: | Adds a register and a sign-extended immediate value and stores the result in a register. |
| Operation: | ```$rt = $rs + imm;```<br>```advance_pc();``` |
| Syntax: | `addiu $rd, $rs, immediate` |
| Encoding: | `00000011 rs rt i i i i` |
| Notes: | no overflow |

##### <a name='op-ADDU'/>ADDU: Add variable unsigned

| | |
|---|---|
| Description: | Adds two registers and stores the result in a register. |
| Operation: | ```$rd = $rs + $rt;```<br>```advance_pc();``` |
| Syntax: | `add $rd, $rs, $rt` |
| Encoding: | `00000100 rd rs rt - - -` |
| Notes: | no overflow |

##### <a name='op-AND'/>AND: And

| | |
|---|---|
| Description: | Bitwise ands two registers and stores the result in a register. |
| Operation: | ```$rd = $rs & $rt;```<br>```advance_pc();``` |
| Syntax: | `and $rd, $rs, $rt` |
| Encoding: | `00000101 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-ANDI'/>ANDI: And immediate

| | |
|---|---|
| Description: | Bitwise ands a register and an immediate value and stores the result in a register. |
| Operation: | ```$rd = $rs & imm;```<br>```advance_pc();``` |
| Syntax: | `andi $rd, $rs, imm` |
| Encoding: | `00000110 rd rs i i i i` |
| Notes: |  |

##### <a name='op-BEQ'/>BEQ: Branch on equal

| | |
|---|---|
| Description: | Branches if the two registers are equal. |
| Operation: | ```if $rs == $rt```<br>```  advance_pc(offset << 2);```<br>```else advance_pc();``` |
| Syntax: | `beq $rs, $rt, offset ` |
| Encoding: | `00000111 rs rt i i i i` |
| Notes: |  |

##### <a name='op-BGEZ'/>BGEZ: Branch on greater than or equal to zero

| | |
|---|---|
| Description: | Branches if the register is greater than or equal to zero. |
| Operation: | ```if $rs >= 0```<br>```  advance_pc(offset << 2);```<br>```else advance_pc();``` |
| Syntax: | `bgez $rs, offset ` |
| Encoding: | `00001000 rs - i i i i` |
| Notes: |  |

##### <a name='op-BGEZAL'/>BGEZAL: Branch on greater than or equal to zero and link

| | |
|---|---|
| Description: | Branches if the register is greater than or equal to zero and saves the return address in $31. |
| Operation: | ```if $rs >= 0 { ```<br>```  $31 = PC + 12 (or nPC + 8);```<br>```  advance_pc(offset << 2));```<br>```} else advance_pc();``` |
| Syntax: | `bgezal $rs, offset ` |
| Encoding: | `00001001 rs - i i i i` |
| Notes: |  |

##### <a name='op-BGTZ'/>BGTZ: Branch on greater than zero

| | |
|---|---|
| Description: | Branches if the register is greater than zero. |
| Operation: | ```if $rs > 0```<br>```  advance_pc(offset << 2);```<br>```else advance_pc();``` |
| Syntax: | `bgtz $rs, offset ` |
| Encoding: | `00001010 rs - i i i i` |
| Notes: |  |

##### <a name='op-BLEZ'/>BLEZ: Branch on less than on equal to zero

| | |
|---|---|
| Description: | Branches if the register is less than or equal to zero. |
| Operation: | ```if $rs <= 0```<br>```  advance_pc(offset << 2);```<br>```else advance_pc();``` |
| Syntax: | `blez $rs, offset ` |
| Encoding: | `00001011 rs - i i i i` |
| Notes: |  |

##### <a name='op-BLTZ'/>BLTZ: Branch on less than zero

| | |
|---|---|
| Description: | Branches if the register is less than zero. |
| Operation: | ```if $rs < 0```<br>```  advance_pc(offset << 2);```<br>```else advance_pc();``` |
| Syntax: | `bltz $rs, offset ` |
| Encoding: | `00001100 rs - i i i i` |
| Notes: |  |

##### <a name='op-BLTZAL'/>BLTZAL: Branch on less than zero and link

| | |
|---|---|
| Description: | Branches if the register is less than zero and saves the return address in $31. |
| Operation: | ```if $rs < 0 {```<br>```  $31 = PC + 12 (or nPC + 8);```<br>```  advance_pc(offset << 2);```<br>```} else advance_pc();``` |
| Syntax: | `bltzal $rs, offset ` |
| Encoding: | `00001101 rs - i i i i` |
| Notes: |  |

##### <a name='op-BNE'/>BNE: Branch on not equal

| | |
|---|---|
| Description: | Branches if the two registers are not equal. |
| Operation: | ```if $rs != $rt```<br>```  advance_pc (offset << 2);```<br>```else advance_pc();``` |
| Syntax: | `bne $rs, $rt, offset ` |
| Encoding: | `00001110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-DIV'/>DIV: Divide

| | |
|---|---|
| Description: | Divides $rs by $rt and stores the quotient in $lo and the remainder in $hi. |
| Operation: | ```$lo = $rs / $rt;```<br>```$hi = $rs % $rt;```<br>```advance_pc();``` |
| Syntax: | `div $rs, $rt` |
| Encoding: | `00001111 rs rt - - - -` |
| Notes: |  |

##### <a name='op-DIVU'/>DIVU: Divide unsigned

| | |
|---|---|
| Description: | Divides $rs by $rt and stores the quotient in $lo and the remainder in $hi. |
| Operation: | ```$lo = $rs / $rt;```<br>```$hi = $rs % $rt;```<br>```advance_pc();``` |
| Syntax: | `divu $rs, $rt` |
| Encoding: | `00010000 rs rt - - - -` |
| Notes: |  |

##### <a name='op-J'/>J: Jump

| | |
|---|---|
| Description: | Jumps to the calculated address. |
| Operation: | ```PC = nPC;```<br>```nPC = (PC & 0xf0000000) | (target << 2);``` |
| Syntax: | `j target` |
| Encoding: | `00010001 i i i i i i` |
| Notes: |  |

##### <a name='op-JAL'/>JAL: Jump and link

| | |
|---|---|
| Description: | Jumps to the calculated address and stores the return address in $31. |
| Operation: | ```$31 = PC + 8 (or nPC + 4);```<br>```PC = nPC;```<br>```nPC = (PC & 0xf0000000) | (target << 2);``` |
| Syntax: | `jal target` |
| Encoding: | `00010010 i i i i i i` |
| Notes: | register $31 used as link target |

##### <a name='op-JR'/>JR: Jump register

| | |
|---|---|
| Description: | Jump to the address contained in register $rs. |
| Operation: | ```PC = nPC;```<br>```nPC = $rs; ``` |
| Syntax: | `jr $rs` |
| Encoding: | `00010011 rs - - - - -` |
| Notes: |  |

##### <a name='op-LB'/>LB: Load byte

| | |
|---|---|
| Description: | A byte is loaded into a register from the specified address at the offset given by the immediate value. |
| Operation: | ```$rd = MEM[$rs + offset];```<br>```advance_pc();``` |
| Syntax: | `lb $rd, offset($rs)` |
| Encoding: | `00010100 rd rs i i i i` |
| Notes: |  |

##### <a name='op-LUI'/>LUI: Load upper immediate

| | |
|---|---|
| Description: | The immediate value is shifted left 16 bits and stored in the register. The lower 16 bits are zeroes. |
| Operation: | ```$rd = (imm << 16);```<br>```advance_pc();``` |
| Syntax: | `lui $rd, imm` |
| Encoding: | `00010101 rd - i i i i` |
| Notes: |  |

##### <a name='op-LW'/>LW: Load word

| | |
|---|---|
| Description: | A word is loaded into a register from the specified address at the offset given by the immediate value. |
| Operation: | ```$rd = MEM[$rs + offset];```<br>```advance_pc();``` |
| Syntax: | `lw $rd, offset($rs)` |
| Encoding: | `00010110 rd rs i i i i` |
| Notes: |  |

##### <a name='op-MFHI'/>MFHI: Move from HI to register

| | |
|---|---|
| Description: | The contents of register $hi are moved to the specified register.  |
| Operation: | ```$rd = $hi;```<br>```advance_pc();``` |
| Syntax: | `mfhi $rd` |
| Encoding: | `00010111 rd - - - - -` |
| Notes: |  |

##### <a name='op-MFLO'/>MFLO: Move from LO to register

| | |
|---|---|
| Description: | The contents of register $lo are moved to the specified register. |
| Operation: | ```$rd = $lo;```<br>```advance_pc();``` |
| Syntax: | `mflo $rd` |
| Encoding: | `00011000 rd - - - - -` |
| Notes: |  |

##### <a name='op-MULT'/>MULT: Multiply signed

| | |
|---|---|
| Description: | Multiplies $rs by $rt and stores the result in $lo.  |
| Operation: | ```$rd = $rs * $rt;```<br>```advance_pc();``` |
| Syntax: | `mult $rd, $rs, $rt` |
| Encoding: | `00011001 rd rs rt - - -` |
| Notes: | with overflow |

##### <a name='op-MULTU'/>MULTU: Multiply unsigned

| | |
|---|---|
| Description: | Multiplies $rs by $rt and stores the result in $lo.  |
| Operation: | ```$rd = $rs * $rt;```<br>```advance_pc();``` |
| Syntax: | `mult $rd, $rs, $rt` |
| Encoding: | `00011010 rd rs rt - - -` |
| Notes: | no overflow |

##### <a name='op-NOOP'/>NOOP: No operation

| | |
|---|---|
| Description: | Performs no operation.  |
| Operation: | ```advance_pc();``` |
| Syntax: | `noop` |
| Encoding: | `00011011 - - - - -` |
| Notes: |  |

##### <a name='op-OR'/>OR: Or

| | |
|---|---|
| Description: | Bitwise logical ors registers given by $rs and $rt and stores the result in $rd register. |
| Operation: | ```$rd = $rs | $rt;```<br>```advance_pc();``` |
| Syntax: | `or $rd, $rs, $rt` |
| Encoding: | `00011100 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-ORI'/>ORI: Or immediate

| | |
|---|---|
| Description: | Bitwise ors register $rs and an immediate value and stores the result in $rd register |
| Operation: | ```$rd = $rs | imm;```<br>```advance_pc();``` |
| Syntax: | `ori $rd, $rs, imm` |
| Encoding: | `00011101 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SB'/>SB: Store byte

| | |
|---|---|
| Description: | The least significant byte of $rt is stored at the specified address at an offset given by the immediate value.  |
| Operation: | ```MEM[$rs + offset] = (0xff & $rt);```<br>```advance_pc();``` |
| Syntax: | `sb $rt, offset($rs)` |
| Encoding: | `00011110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-SLL'/>SLL: Shift left logical

| | |
|---|---|
| Description: | Shifts $rs register value left by the shift amount imm and places the result in $rd register. Zeroes are shifted in. |
| Operation: | ```$rd = $rs << imm;```<br>```advance_pc(); ``` |
| Syntax: | `sll $rd, $rs, imm` |
| Encoding: | `00011111 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SLLV'/>SLLV: Shift left logical variable

| | |
|---|---|
| Description: | Shifts $rs register value left by the value in $rt register and places the result in $rd. Zeroes are shifted in.  |
| Operation: | ```$rd = $rs << $rt;```<br>```advance_pc(); ``` |
| Syntax: | `sllv $rd, $rs, $rt` |
| Encoding: | `00100000 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SLT'/>SLT: Set if less than signed

| | |
|---|---|
| Description: | If $rs is less than $rt, $rd is set to one. It gets zero otherwise.  |
| Operation: | ```if $rs < $rt```<br>```  $rd = 1; ```<br>```else $rd = 0;```<br>```advance_pc();``` |
| Syntax: | `slt $rd, $rs, $rt` |
| Encoding: | `00100001 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SLTI'/>SLTI: Set if less than immediate signed

| | |
|---|---|
| Description: | If $rs is less than immediate, $rd is set to one. It gets zero otherwise.  |
| Operation: | ```if $rs < imm ```<br>```  $rt = 1;```<br>```else $rt = 0;```<br>```advance_pc(); ``` |
| Syntax: | `slti $rd, $rs, imm` |
| Encoding: | `00100010 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SLTIU'/>SLTIU: Set if less than immediate unsigned

| | |
|---|---|
| Description: | If $rs is less than the unsigned immediate, $rd is set to one. It gets zero otherwise.  |
| Operation: | ```if $rs < imm ```<br>```  $rt = 1;```<br>```else $rt = 0;```<br>```advance_pc();``` |
| Syntax: | `sltiu $rd, $rt, imm` |
| Encoding: | `00100011 rd rs i i i i` |
| Notes: |  |

##### <a name='op-SLTU'/>SLTU: Set if less than unsigned

| | |
|---|---|
| Description: | If $rs is less than $rt, $rd is set to one. It gets zero otherwise.  |
| Operation: | ```if $rs < $rt```<br>```  $rd = 1;```<br>```else $rd = 0;```<br>```advance_pc();``` |
| Syntax: | `sltu $rd, $rs, $rt` |
| Encoding: | `00100100 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SRA'/>SRA: Shift right arithmetic

| | |
|---|---|
| Description: | Shifts a register value right by the shift amount (imm) and places the value in the destination register. The sign bit is shifted in.  |
| Operation: | ```$rd = $rs >> imm;```<br>```advance_pc();``` |
| Syntax: | `sra $rd, $rs, imm` |
| Encoding: | `00100101 rd rs i i i i` |
| Notes: | signed, sign bit is shifted in. MIPS uses shamt field in instruction instead of imm. |

##### <a name='op-SRL'/>SRL: Shift right logical

| | |
|---|---|
| Description: | Shifts a register value right by the shift amount (imm) and places the value in the destination register. Zeroes are shifted in.  |
| Operation: | ```$rd = $rs >> imm;```<br>```advance_pc();``` |
| Syntax: | `srl $rd, $rs, imm` |
| Encoding: | `00100110 rd rs i i i i` |
| Notes: | unsigned, zero is shifted in. MIPS uses shamt field in instruction instead of imm. |

##### <a name='op-SRLV'/>SRLV: Shift right logical variable

| | |
|---|---|
| Description: | Shifts a register value right by the amount specified in $rs and places the value in the destination register. Zeroes are shifted in.  |
| Operation: | ```$rd = $rs >> $rt;```<br>```advance_pc();``` |
| Syntax: | `srlv $rd, $rs, $rt` |
| Encoding: | `00100111 rd rs rt - - -` |
| Notes: | unsigned, zero is shifted in |

##### <a name='op-SRAV'/>SRAV: Shift right arithmetic variable

| | |
|---|---|
| Description: | Shifts a register value right by the amount specified in $rs and places the value in the destination register. The sign bit is shifted in.  |
| Operation: | ```$rd = $rs >> $rt;```<br>```advance_pc();``` |
| Syntax: | `srav $rd, $rs, $rt` |
| Encoding: | `00101000 rd rs rt - - -` |
| Notes: | signed, sign bit is shifted in. Not part of MIPS instructions. |

##### <a name='op-SUB'/>SUB: Subtract

| | |
|---|---|
| Description: | Subtracts two registers and stores the result in a register. |
| Operation: | ```$rd = $rs - $rt;```<br>```advance_pc();``` |
| Syntax: | `sub $rd, $rs, $rt` |
| Encoding: | `00101001 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SUBU'/>SUBU: Subtract unsigned

| | |
|---|---|
| Description: | Subtracts two registers and stores the result in a register. |
| Operation: | ```$rd = $rs - $rt;```<br>```advance_pc();``` |
| Syntax: | `subu $rd, $rs, $rt` |
| Encoding: | `00101010 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-SW'/>SW: Store word

| | |
|---|---|
| Description: | The contents of $rt is stored at the specified address using the offset given by the immediate value.  |
| Operation: | ```MEM[$rs + offset] = $rt;```<br>```advance_pc();``` |
| Syntax: | `sw $rt, offset($rs)` |
| Encoding: | `00101011 rs rt i i i i` |
| Notes: |  |

##### <a name='op-SYSCALL'/>SYSCALL: System call

| | |
|---|---|
| Description: | nop for the time being. |
| Operation: | ```noop;``` |
| Syntax: | `syscall` |
| Encoding: | `00101100 ` |
| Notes: |  |

##### <a name='op-XOR'/>XOR: Xor

| | |
|---|---|
| Description: | Exclusive ors two registers and stores the result in a register. |
| Operation: | ```$rd = $rs ^ $rt;```<br>```advance_pc();``` |
| Syntax: | `xor $rd, $rs, $rt ` |
| Encoding: | `00101101 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-XORI'/>XORI: Xor immediate

| | |
|---|---|
| Description: | Bitwise exclusive ors a register and an immediate value and stores the result in a register. |
| Operation: | ```$rd = $rs ^ imm;```<br>```advance_pc();``` |
| Syntax: | `xori $rt, $rs, imm ` |
| Encoding: | `00101110 rs rt i i i i` |
| Notes: |  |

##### <a name='op-MOD'/>MOD: Mod

| | |
|---|---|
| Description: | Modulo remainder of $rs by $rt and sets $rd to result. |
| Operation: | ```div $rs, $rt;```<br>```mfhi $rd;```<br>```advance_pc();``` |
| Syntax: | `mod $rd, $rs, $rt` |
| Encoding: | `00101111 rd rs rt - - -` |
| Notes: | Special EVM op |

##### <a name='op-SMOD'/>SMOD: Mod signed

| | |
|---|---|
| Description: | Signed modulo remainder of $rs by $rt and sets $rd to result. |
| Operation: | ```sdiv $rs, $rt;```<br>```mfhi $rd;```<br>```advance_pc();``` |
| Syntax: | `smod $rd, $rs, $rt` |
| Encoding: | `00110000 rd rs rt - - -` |
| Notes: | Special EVM op |

##### <a name='op-ADDMOD'/>ADDMOD: Add then mod

| | |
|---|---|
| Description: | Takes $rs and $rt register values, adds them, then takes the modulo of that result using the $ru register value. Sets the $rd register value to the result. |
| Operation: | ```add $rd, $rs, $rt;```<br>```div $rd, $ru;```<br>```mfhi $rd;```<br>```advance_pc();``` |
| Syntax: | `addmod $rd, $rs, $rt, $ru` |
| Encoding: | `00110001 rd rs rt ru - -` |
| Notes: | Special EVM op |

##### <a name='op-MULMOD'/>MULMOD: Multiply then mod

| | |
|---|---|
| Description: | Takes $rs and $rt register values, multiplies them, then takes the modula of that result using $ru. Sets $rd register value to the result. |
| Operation: | ```mul $rd, $rs, $rt;```<br>```div $rd, $ru;```<br>```mfhi $rd;```<br>```advance_pc();``` |
| Syntax: | `mulmod $rd, $rs, $rt, $ru` |
| Encoding: | `00110010 rd rs rt ru - -` |
| Notes: | Special EVM op |

##### <a name='op-EXP'/>EXP: Exponentiate

| | |
|---|---|
| Description: | Takes $rs and $rt register values, then sets $rd register value to the result of $rs taken to the power of $rt. |
| Operation: | ```for (int i = 0; i < $rt) ```<br>```  mul $rd, $rd, $rs;```<br>```advance_pc();``` |
| Syntax: | `exp $rd, $rs, $rt` |
| Encoding: | `00110011 rd rs rt - - -` |
| Notes: | Special EVM op |

##### <a name='op-MODI'/>MODI: Mod immediate

| | |
|---|---|
| Description: | Modulo remainder of $rs using immediate value and places result in $rd. |
| Operation: | ```div $rs, imm;```<br>```mfhi $rd;```<br>```advance_pc();``` |
| Syntax: | `modi $rd, $rs, imm` |
| Encoding: | `00110100 rd rs i i i i` |
| Notes: | Special EVM op |

##### <a name='op-SMODI'/>SMODI: Mod signed immediate

| | |
|---|---|
| Description: | Signed modulo remainder of $rs using immediate value and places result in $rd. |
| Operation: | ```sdiv $rs, imm;```<br>```mfhi $rd;```<br>```advance_pc();``` |
| Syntax: | `smodi $rd, $rs, imm` |
| Encoding: | `00110101 rd rs i i i i` |
| Notes: | Special EVM op |

##### <a name='op-EXPI'/>EXPI: Exponentiate immediate

| | |
|---|---|
| Description: | Takes $rs value, then sets $rd register value to the result of $rs taken to the power of imm. |
| Operation: | ```for (int i = 0; i < $rt) ```<br>```  mul $rd, $rd, imm;```<br>```advance_pc();``` |
| Syntax: | `expi $rd, $rs, imm` |
| Encoding: | `00110110 rd rt i i i i` |
| Notes: | Special EVM op |



#### <a name='op-group-id-64'/>Ethereum Operands

##### <a name='op-GAS'/>GAS: 

| | |
|---|---|
| Description: | Sets the $rd register value to the numeric value of gas remaining. |
| Operation: | ```$rd = $gas;```<br>```advance_pc();``` |
| Syntax: | `gas $rd` |
| Encoding: | `10000000 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-GASLIMIT'/>GASLIMIT: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's gas limit (as set by the client). |
| Operation: | ```$rd = $gaslimit;```<br>```advance_pc();``` |
| Syntax: | `gaslimit $rd` |
| Encoding: | `10001011 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-GASPRICE'/>GASPRICE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the gas price that was set in the originating transaction. |
| Operation: | ```$rd = $gasprice;```<br>```advance_pc();``` |
| Syntax: | `gasprice $rd` |
| Encoding: | `10001100 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-BALANCE'/>BALANCE: 

| | |
|---|---|
| Description: | Takes the $rs register value, which is an address, and then sets the $rd register value to the balance of given address. |
| Operation: | ```$rd = $balance[$rs];```<br>```advance_pc();``` |
| Syntax: | `balance $rd, $rs` |
| Encoding: | `10000001 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-ADDRESS'/>ADDRESS: 

| | |
|---|---|
| Description: | Sets the $rd register value to the address of the currently executing account. |
| Operation: | ```$rd = $address;```<br>```advance_pc();``` |
| Syntax: | `address $rd` |
| Encoding: | `10000010 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-ORIGIN'/>ORIGIN: 

| | |
|---|---|
| Description: | Sets the $rd register value to the execution origin address. This is the sender of the original transaction. Note: it is never an account with non-empty associated code. |
| Operation: | ```$rd = $origin;```<br>```advance_pc();``` |
| Syntax: | `origin $rd` |
| Encoding: | `10000011 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLER'/>CALLER: 

| | |
|---|---|
| Description: | Sets the $rd register value to the address of the account that is directly responsible for this execution. |
| Operation: | ```$rd = $caller;```<br>```advance_pc();``` |
| Syntax: | `caller $rd` |
| Encoding: | `10000100 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLVALUE'/>CALLVALUE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the value deposited by the instruction/transaction responsible for this execution. |
| Operation: | ```$rd = $value;```<br>```advance_pc();``` |
| Syntax: | `callvalue $rd` |
| Encoding: | `10000101 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-BLOCKHASH'/>BLOCKHASH: 

| | |
|---|---|
| Description: | Takes 64-bit block number value from memory at address given by register $rs, and sets $rd register value to the hash of the block that at that block number. |
| Operation: | ```$rd = $blockhash(MEM[$rs..$rs+8]);```<br>```advance_pc();``` |
| Syntax: | `blockhash $rd, $rs` |
| Encoding: | `10000110 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-COINBASE'/>COINBASE: 

| | |
|---|---|
| Description: | Sets $rd register value to the beneficiary address (as set by the client). |
| Operation: | ```$rd = $coinbase;```<br>```advance_pc();``` |
| Syntax: | `coinbase $rd` |
| Encoding: | `10000111 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-TIMESTAMP'/>TIMESTAMP: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's timestamp (as set by the client). |
| Operation: | ```$rd = $timestamp;```<br>```advance_pc();``` |
| Syntax: | `timestamp $rd` |
| Encoding: | `10001000 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-NUMBER'/>NUMBER: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's number (as set by the client). |
| Operation: | ```$rd = $number;```<br>```advance_pc();``` |
| Syntax: | `number $rd` |
| Encoding: | `10001001 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-DIFFICULTY'/>DIFFICULTY: 

| | |
|---|---|
| Description: | Sets $rd register value to the block's difficulty (as set by the client). |
| Operation: | ```$rd = $difficulty;```<br>```advance_pc();``` |
| Syntax: | `difficulty $rd` |
| Encoding: | `10001010 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLDATASIZE'/>CALLDATASIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of a message call's input data. |
| Operation: | ```$rd = size($calldata);```<br>```advance_pc();``` |
| Syntax: | `calldatasize $rd` |
| Encoding: | `10001101 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLDATALOAD'/>CALLDATALOAD: 

| | |
|---|---|
| Description: | Takes the $rs register value, which represents the byte offset of a message call's input data, and sets the $rd register value to the 256-bit value at that offset of the calldata. |
| Operation: | ```$rd = $calldata[$rs];```<br>```advance_pc();``` |
| Syntax: | `calldataload $rd, $rs` |
| Encoding: | `10001110 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLDATACOPY'/>CALLDATACOPY: 

| | |
|---|---|
| Description: | Copy calldata into memory. The $rs value represents the offset in memory to start copying values from the call data which is offset by the $rd value. The number of bytes copied is equal to the $rt value. |
| Operation: | ```MEM[$rd] = $calldata[$rs..$rs+$rt];```<br>```advance_pc();``` |
| Syntax: | `calldatacopy $rd, $rs, $rt` |
| Encoding: | `10001111 rd rs rt - - -` |
| Notes: | EVM component |

##### <a name='op-CODESIZE'/>CODESIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of the code being executed. |
| Operation: | ```$rd = $codesize($address);```<br>```advance_pc();``` |
| Syntax: | `codesize $rd` |
| Encoding: | `10010000 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-CODECOPY'/>CODECOPY: 

| | |
|---|---|
| Description: | Copy code currently being executed into memory. The $rs value represents the offset in memory to start copying values from the code data which is $rd by the second value. The number of bytes copied is equal to the $rt value. |
| Operation: | ```MEM[$rd] = $code[$rs..$rs+$rt];```<br>```advance_pc();``` |
| Syntax: | `codecopy $rd, $rs, $rt` |
| Encoding: | `10010001 rd rs rt - - -` |
| Notes: | EVM component |

##### <a name='op-EXTCODESIZE'/>EXTCODESIZE: 

| | |
|---|---|
| Description: | Takes the $rs register value, an account address, and sets the $rd register value to the size of the code at that address. |
| Operation: | ```$rd = $codesize($rs);```<br>```advance_pc();``` |
| Syntax: | `extcodesize $rd, $rs` |
| Encoding: | `10010010 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-EXTCODEHASH'/>EXTCODEHASH: 

| | |
|---|---|
| Description: | Takes $rs register value, the address, and sets $rd register value to the hash of the code at that address, else set it to 0. |
| Operation: | ```$rd = $codehash[$rs];```<br>```advance_pc();``` |
| Syntax: | `extcodehash $rd, $rs` |
| Encoding: | `10010011 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-EXTCODECOPY'/>EXTCODECOPY: 

| | |
|---|---|
| Description: | Like CODECOPY, but the $ru parameter for this operation is the account to copy the code of. |
| Operation: | ```MEM[$rd] = $code[$ru][$rs..$rs+$rt];```<br>```advance_pc();``` |
| Syntax: | `extcodecopy $rd, $ru, $rs, $rt` |
| Encoding: | `10010100 rd rs rt ru - -` |
| Notes: | EVM component |

##### <a name='op-RETURNDATASIZE'/>RETURNDATASIZE: 

| | |
|---|---|
| Description: | Sets the $rd register value to the size of the returned data from the last call in the current execution. |
| Operation: | ```$rd = size($returndata);```<br>```advance_pc();``` |
| Syntax: | `returndatasize $rd` |
| Encoding: | `10010101 rd - - - - -` |
| Notes: | EVM component |

##### <a name='op-RETURNDATACOPY'/>RETURNDATACOPY: 

| | |
|---|---|
| Description: | Copies the return data into memory. $rs value specifies the address offset of the return data, the $rd value specifies the offset in memory to copy the data to, and the $rt value gives the number of bytes to copy. |
| Operation: | ```MEM[$rd] = $returndata[$rs..$rs+$rt];```<br>```advance_pc();``` |
| Syntax: | `returndatacopy $rd, $rs, $rt` |
| Encoding: | `10010110 rd rs rt - - -` |
| Notes: | EVM component |

##### <a name='op-CALL'/>CALL: 

| | |
|---|---|
| Description: | Message call into an account where $rs points to a sequence of words in memory that are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the $rd register's value if the call completes successfully. |
| Operation: | ```pre-compile``` |
| Syntax: | `call $rd, $rs` |
| Encoding: | `10010111 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-CALLCODE'/>CALLCODE: 

| | |
|---|---|
| Description: | Message call into an account with the current account's code where $rs points to a sequence of words in memory that are ordered as follows: gas, to, value, in offset, in size, out offset, out size. The value 1 is set as the $rd register's value if the call completes successfully. |
| Operation: | ```pre-compile``` |
| Syntax: | `callcode $rd, $rs` |
| Encoding: | `10011000 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-DELEGATECALL'/>DELEGATECALL: 

| | |
|---|---|
| Description: | Message-call into this account with an alternative account's code, but persisting the current values for sender and value. |
| Operation: | ```pre-compile``` |
| Syntax: | `delegatecall $rd, $rs` |
| Encoding: | `10011001 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-STATICCALL'/>STATICCALL: 

| | |
|---|---|
| Description: | Static message-call into an account. |
| Operation: | ```pre-compile``` |
| Syntax: | `staticcall $rd, $rs` |
| Encoding: | `10011010 rd rs - - - -` |
| Notes: | EVM component |

##### <a name='op-RETURN'/>RETURN: 

| | |
|---|---|
| Description: | Returns values from memory using first register value as memory offset whose size is given by the second register value. Program execution then halts. |
| Operation: | ```pre-compile``` |
| Syntax: | `return $rs, $rt` |
| Encoding: | `10011011 rs rt - - - -` |
| Notes: | EVM component |

##### <a name='op-CREATE2'/>CREATE2: 

| | |
|---|---|
| Description: | Create a new account, like CREATE, but use the salt of the fourth register value. |
| Operation: | ```pre-compile``` |
| Syntax: | `create2 $rd, $rs, $rt, $ru, $rv` |
| Encoding: | `10011100 rd rs rt ru rv -` |
| Notes: | EVM component |

##### <a name='op-REVERT'/>REVERT: 

| | |
|---|---|
| Description: | Halt execution reverting state changes but returning data and remaining gas. |
| Operation: | ```pre-compile``` |
| Syntax: | `revert $rs, $rt` |
| Encoding: | `10011101 rs rt - - - -` |
| Notes: | EVM component |

##### <a name='op-KECCAK'/>KECCAK: keccak-256

| | |
|---|---|
| Description: | Keccak-256 hash. Takes $rs and $rt register values, values which represent the range of at least one item in memory to compute the Keccak-256 hash of. The $rs value is inclusive and the $rt value is exclusive; this means that the $rt value must be greater than the $rs value. The $rd register value is set to the hash value. Note: that this opcode mnemonic differs from the SHA3 opcode as specified in the yellow paper because SHA3 is not the same as Keccak. We do this to allow proper SHA3 operations in the Fuel VM.  |
| Operation: | ```$rd = keccak(MEM[$rs:$rt]);```<br>```advance_pc(); ``` |
| Syntax: | `keccak $rd, $rs, $rt` |
| Encoding: | `10011110 rd rs rt - - -` |
| Notes: |  |

##### <a name='op-LOG0'/>LOG0: 

| | |
|---|---|
| Description: | Takes $rs and $rt register values and records a log entry with no topics based on the memory offset of the $rs value, with a length given by the $rt value. |
| Operation: | ```$log.push(':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();``` |
| Syntax: | `log0 $rs, $rt` |
| Encoding: | `10011111 rs rt - - - -` |
| Notes: | EVM component |

##### <a name='op-LOG1'/>LOG1: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru value.  |
| Operation: | ```$log.push($ru + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();``` |
| Syntax: | `log1 $rs, $rt, $ru` |
| Encoding: | `10100000 rs rt ru - - -` |
| Notes: | EVM component |

##### <a name='op-LOG2'/>LOG2: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv values.  |
| Operation: | ```$log.push($ru + $rv + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();``` |
| Syntax: | `log2 $rs, $rt, $ru, $rv` |
| Encoding: | `10100001 rs rt ru rv - -` |
| Notes: | EVM component |

##### <a name='op-LOG3'/>LOG3: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw values.  |
| Operation: | ```$log.push($ru + $rv + $rw + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();``` |
| Syntax: | `log3 $rs, $rt, $ru, $rv, $rw` |
| Encoding: | `10100010 rs rt ru rv rw -` |
| Notes: | EVM component |

##### <a name='op-LOG4'/>LOG4: 

| | |
|---|---|
| Description: | Take $rs and $rt register values and records a log entry with data copied from memory at an offset given by $rs, whose length is given by $rt. The topic of the log entry is given by the $ru, $rv, $rw, $rx values.  |
| Operation: | ```$log.push($ru + $rv + $rw + $rx + ':' + MEM[$rs..$rs+$rt]);```<br>```advance_pc();``` |
| Syntax: | `log4 $rs, $rt, $ru, $rv, $rw, $rx` |
| Encoding: | `10100011 rs rt ru rv rw rx` |
| Notes: | EVM component |



#### <a name='op-group-id-102'/>EIP-615: Subroutines and Static Jumps

##### <a name='op-JUMPTO'/>JUMPTO: Jump to a specific code address

| | |
|---|---|
| Description: | Use immediate addressing for static analysis. |
| Operation: | ```nPC = imm;``` |
| Syntax: | `jumpto imm` |
| Encoding: | `10101010 - - i i i i` |
| Notes: |  |

##### <a name='op-JUMPIF'/>JUMPIF: Jump to a specific code address if $compare flag is set

| | |
|---|---|
| Description: | Use immediate addressing for static analysis. Depends on special flags for jump. |
| Operation: | ```if ($compare == 1)```<br>```  nPC = imm;```<br>```else advance_pc();``` |
| Syntax: | `jumpif imm` |
| Encoding: | `10101011 - - i i i i` |
| Notes: |  |

##### <a name='op-JUMPV'/>JUMPV: Jumps to destination specified as zero-based BEGINDATA offset of destination vectors, where offset is given by $rs

| | |
|---|---|
| Description: | Use immediate addressing for static analysis. Depends on BEGINDATA operation for specifying vector of destinations.  |
| Operation: | ```if ($rs >= n)```<br>```  nPC = DATA[v_data_offset+n-1]; ```<br>```else nPC = DATA[v_data_offset+$rs]; ``` |
| Syntax: | `jumpv n, v_data_offset, $rs` |
| Encoding: | `10101100 rs n i i i i` |
| Notes: |  |

##### <a name='op-JUMPSUB'/>JUMPSUB: Jumps to labeled subroutine

| | |
|---|---|
| Description: | Use immediate addressing for static analysis. Depends on prior declaration of BEGINSUB for destination to be considered valid, and BEGINSUB declarations for all address must be validated at compile-time.  |
| Operation: | ```$callstack.push(nPC);```<br>```nPC = imm; ``` |
| Syntax: | `jumpsub imm` |
| Encoding: | `10101101 - - i i i i` |
| Notes: |  |

##### <a name='op-JUMPSUBV'/>JUMPSUBV: Jumps to destination specified as zero-based BEGINDATA offset of BEGINSUB vectors, where offset is given by $rs

| | |
|---|---|
| Description: | Use immediate addressing for static analysis. Depends on prior declaration of BEGINSUB for destination to be considered valid, and BEGINSUB declarations for all address must be validated at compile-time. Depends on BEGINDATA operation for specifying vector of destinations.  |
| Operation: | ```if ($rs >= n) { ```<br>```  $callstack.push(nPC);```<br>```  nPC = DATA[v_data_offset+n-1];```<br>```} else { ```<br>```  $callstack.push(nPC);```<br>```  nPC = DATA[v_data_offset+$rs];```<br>```} ``` |
| Syntax: | `jumpsubv n, v_data_offset, $rs` |
| Encoding: | `10101110 rs n i i i i` |
| Notes: |  |

##### <a name='op-RETURNSUB'/>RETURNSUB: Returns from subroutine execution to instruction following subroutine call.

| | |
|---|---|
| Description: | Use a call stack to track jump addresses and return addresses. Return variables are stored in registers or memory as needed by the subroutine. Unlike stack-based approach, since return sub continues execution at instruction following subroutine call, code can undergo static analysis. |
| Operation: | ```nPC = $callstack.pop();``` |
| Syntax: | `returnsub` |
| Encoding: | `10101111 - - - - - -` |
| Notes: |  |

##### <a name='op-GETLOCAL'/>GETLOCAL: Get local variable value

| | |
|---|---|
| Description: | Allocate local variables in memory and use a memory pointer for access. This overlaps with SB/SW and LB/LW (store/load byte/word) opcodes from above (and MIPS), but this can be an offset into memory addressing used for local variables. |
| Operation: | ```$rs = MEM[$subroutinemarker+n];```<br>```advance_pc();``` |
| Syntax: | `getlocal $rd, n` |
| Encoding: | `10110000 rd - i i i i` |
| Notes: |  |

##### <a name='op-PUTLOCAL'/>PUTLOCAL: Sets local variable value

| | |
|---|---|
| Description: | Allocate local variables in memory and use a memory pointer for access. This overlaps with SB/SW and LB/LW (store/load byte/word) opcodes from above (and MIPS), but this can be an offset into memory addressing used for local variables. |
| Operation: | ```MEM[$subroutinemarker+n] = $rs;```<br>```advance_pc();``` |
| Syntax: | `putlocal $rs, n` |
| Encoding: | `10110001 rd - i i i i` |
| Notes: |  |

##### <a name='op-BEGINSUB'/>BEGINSUB: Subroutine label declaration

| | |
|---|---|
| Description: | Required to declare subroutines, which are then used for checking validity of subroutine-based jump operations. Updates execution context but oes not change state. |
| Operation: | ```SUBS.push(PC);```<br>```advance_pc();``` |
| Syntax: | `beginsub n_args, n_returns` |
| Encoding: | `10110010 - - - - args returns` |
| Notes: |  |

##### <a name='op-BEGINDATA'/>BEGINDATA: Data label declaration

| | |
|---|---|
| Description: | Marker generated by compiler to help define interpreter execution context.  Updates execution context but oes not change state. |
| Operation: | `````` |
| Syntax: | `begindata` |
| Encoding: | `10110011 ` |
| Notes: |  |



#### <a name='op-group-id-114'/>EIP-616: SIMD Operations 

##### <a name='op-SIMDADD'/>SIMDADD: Add. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See ADD``` |
| Syntax: | `simdadd $rd, $rs, $rt` |
| Encoding: | `10110100 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDMUL'/>SIMDMUL: Multiply. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See MUL``` |
| Syntax: | `simdmul $rd, $rs, $rt` |
| Encoding: | `10110101 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMSUB'/>SIMSUB: Subtract. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SUB``` |
| Syntax: | `simdsub $rd, $rs, $rt` |
| Encoding: | `10110110 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDDIV'/>SIMDDIV: Divide. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See DIV``` |
| Syntax: | `simddiv $rd, $rs, $rt` |
| Encoding: | `10110111 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSDIV'/>SIMDSDIV: Signed Division. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SDIV``` |
| Syntax: | `simdsdiv $rd, $rs, $rt` |
| Encoding: | `10111000 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDMOD'/>SIMDMOD: Mod. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See MOD``` |
| Syntax: | `simdmod $rd, $rs, $rt` |
| Encoding: | `10111001 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSMOD'/>SIMDSMOD: Signed Mod. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SMOD``` |
| Syntax: | `simdsmod $rd, $rs, $rt` |
| Encoding: | `10111010 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDLT'/>SIMDLT: Less than. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See LT``` |
| Syntax: | `simdlt $rd, $rs, $rt` |
| Encoding: | `10111011 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDGT'/>SIMDGT: Greater than. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See GT``` |
| Syntax: | `simdgt $rd, $rs, $rt` |
| Encoding: | `10111100 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSLT'/>SIMDSLT: Signed less than. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SLT``` |
| Syntax: | `simdslt $rd, $rs, $rt` |
| Encoding: | `10111101 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSGT'/>SIMDSGT: Signed greater than. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SGT``` |
| Syntax: | `simdsgt $rd, $rs, $rt` |
| Encoding: | `10111110 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDEQ'/>SIMDEQ: Equal. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See EQ``` |
| Syntax: | `simdeq $rd, $rs, $rt` |
| Encoding: | `10111111 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDISZERO'/>SIMDISZERO: Is Zero comparison. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See IsZero``` |
| Syntax: | `simdiszero $rd, $rs, $rt` |
| Encoding: | `11000000 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDAND'/>SIMDAND: Logical AND. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See AND``` |
| Syntax: | `simdand $rd, $rs, $rt` |
| Encoding: | `11000001 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDOR'/>SIMDOR: Logical OR. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See OR``` |
| Syntax: | `simdor $rd, $rs, $rt` |
| Encoding: | `11000010 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDXOR'/>SIMDXOR: Logical XOR. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See XOR``` |
| Syntax: | `simdxor $rd, $rs, $rt` |
| Encoding: | `11000011 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDNOT'/>SIMDNOT: Logical NOT. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See NOT``` |
| Syntax: | `simdnot $rd, $rs, $rt` |
| Encoding: | `11000100 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSHL'/>SIMDSHL: Shift left. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SHL``` |
| Syntax: | `simdshl $rd, $rs, $rt` |
| Encoding: | `11000101 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSHR'/>SIMDSHR: Shift right. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SHR``` |
| Syntax: | `simdshr $rd, $rs, $rt` |
| Encoding: | `11000110 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSAR'/>SIMDSAR: Shift right arithmetic. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See SAR``` |
| Syntax: | `simdsar $rd, $rs, $rt` |
| Encoding: | `11000111 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDROL'/>SIMDROL: Rotate left. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See ROL``` |
| Syntax: | `simdrol $rd, $rs, $rt` |
| Encoding: | `11001000 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDROR'/>SIMDROR: Rotate right. Operate pair-wise on the source elements to compute the destination elements.

| | |
|---|---|
| Description: | The source operands must have the same element type and number of elements. |
| Operation: | ```See ROR``` |
| Syntax: | `simdror $rd, $rs, $rt` |
| Encoding: | `11001001 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDINDEX'/>SIMDINDEX: Access a specific byte from vector

| | |
|---|---|
| Description: | Extracts a word at a given immediate index, given the lane width and count in the context byte. |
| Operation: | ```$rd = MEM[$rs+imm];```<br>```advance_pc();``` |
| Syntax: | `simdindex $rd, $rs, imm` |
| Encoding: | `11001010 C rd rs rt - -` |
| Notes: |  |

##### <a name='op-SIMDSHUFFLE'/>SIMDSHUFFLE: Takes two vectors: a vector to permute and a permutation mask. Returns permusted vector.

| | |
|---|---|
| Description: | The mask must have integral type, and the same number of elements as the source vector. |
| Operation: | ```MEM[$rd] = MEM[$rs[rt[0]]...$rs[rt[rt.length()-1]]];```<br>```advance_pc();``` |
| Syntax: | `simdshuffle $rd, $rs, $rt` |
| Encoding: | `11001100 C rd rs rt - -` |
| Notes: |  |



#### <a name='op-group-id-140'/>MAST Proof mechanisms

##### <a name='op-CHECKPROOF'/>CHECKPROOF: Takes a vector of hashes and checks proof

| | |
|---|---|
| Description: | nnn represents 3 bits for number of hashes used as input to proof checker. iii represents 3 bits for index position of provided hash. $rs represents a pointer to the memory location where the hashes reside. $rt is the root of the hash to check the proof calculation against. |
| Operation: | ```pre-compile``` |
| Syntax: | `checkproof $rd, n, $rs, index, $rt` |
| Encoding: | `11001101 rd rs rt nnniii - -` |
| Notes: |  |



#### <a name='op-group-id-143'/>Covenants: Template-checking mechanisms

##### <a name='op-CHECKTEMPLATE'/>CHECKTEMPLATE: Checks a memory region against a template mask.

| | |
|---|---|
| Description: | Verifies an input value $rs based on mask $rt in memory with size imm, and a root hash $ru, and stores the verification result to $rd.  |
| Operation: | ```if $rs & $rt == 0```<br>```  $rd = 1;```<br>```else $rd = 0;```<br>```advance_pc();``` |
| Syntax: | `checktemplate $rd, $rs, imm, $rt` |
| Encoding: | `11001110 rd rs rt i i i` |
| Notes: | As the immediate value can take 18 bits, the width is sufficient to represent a 32MB template. To support larger templates, additional instructions with additional templates can be encoded statically at compile-time. Also referred to as opcode CheckOutputVerify; we choose a different term as the Fuel VM operates on types other than UTXO outputs. In our implementation, we do not expect a value to be ascribed to be delivered (as would be the case with an output-based tx). An index is provided with the original specification of CheckOutputVerify, however, the output index may vary in the interpreter due to features such as parallelism. |