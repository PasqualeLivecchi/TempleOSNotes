This is mostly for myself, trying to figure out what some Assembly code that is used does. Partially to try to understand x86 Assembly better (I barely know it), and get a better understanding of TempleOS.  
This isn't a guide like Disassembling.md.  

## Compiler/Templates.HC
This seems to be Templates which are used by Compiler for some code. Unsure if it copies out a portion, or just jumps to the labels. It being wrapped in an `asm` block makes me think it's inserted into the code.  

## asm snippets

### _CLAMP_I64 (ClampI64)
Signature declared in KernelB.HH, `_CLAMP_U64 U64 ClampU64(u64 num, u64 lo, u64 hi)`  
Code located in KUtils.HC
```asm
_CLAMP_I64:: ; takes (I64 num, I64 lo, I64 hi)
	PUSH	RBP ; Save the value of RBP
	MOV	RBP,RSP ; Make the pointer to the base of the stack equal to the top of the stack. Dunno why.
    ; Uses these three registers to store the args. Don't know if this is in reverse order.
	MOV	RAX,U64 SF_ARG1[RBP] ; num?
	MOV	RCX,U64 SF_ARG2[RBP] ; lo?
	MOV	RDX,U64 SF_ARG3[RBP] ; hi?

	CMP	RAX,RCX ; cmp num and lo
	CMOVL	RAX,RCX ; Checks if the cmp meant that num was less than lo, if it was then set num=lo

	CMP	RAX,RDX ; cmp num and hi
	CMOVG	RAX,RDX ; Checks if the cmp meant that num was greater than hi, if it was then set num=hi

	POP	RBP ; Restore the saved value ox86f RBP, to not clobber it
	RET1	24 ; returns, and releases 24 bytes from the stack after the return addr is popped
	           ; Since these are I64 they take up 8+8+8=24, so it's cleaning up it's own arguments.
```
HolyC Approximation:
```C
U64 _CLAMP_I64 (I64 num, I64 lo, I64 hi) {
	if (num < lo) {
		num = lo;
	}
	if (num > hi) {
		num = hi;
	}
	return num;
}
```

## _BEQU (BEqu)
Signature declared in KernelB.HH, `_BEQU Bool BEqu(U8 *bit_field, I64 bit, Bool val);`  
Code located in KUtils.HC.  
```asm
_BEQU:: ; (U8 *bit_field, I64 bit, Bool val)
	PUSH	RBP ; Save RBP
	MOV	RBP,RSP ; Move stack pointer upwards

	XOR	RAX,RAX ; clear RAX, dunno why

	; Setup arguments
	MOV	RDX,U64 SF_ARG3[RBP] ; RDX=val
	MOV	RBX,U64 SF_ARG1[RBP] ; RBX=bit_field
	MOV	RCX,U64 SF_ARG2[RBP] ; RCX=bit

	TEST	RDX,RDX ; AND RDX with itself
	JZ	@@5 ; Jump to @@5 if RDX is 0
	; x&x=0 means x must be 0. So this is a test for if it's zero.

	BTS	U64 [RBX],RCX ; CF=bit_field[bit], and Sets bit_field[bit]=1
	JMP	@@10
; If val is zero:
@@5:	BTR	U64 [RBX],RCX ; Set the bit to Zero
; If val is not Zero:
@@10:	ADC	AL,0 ; AL=AL+0+CF, and sets CF to if it carried. I don't know why it does this.

	POP	RBP ; Restore RBP
	RET1	24 ; size(U8*)=8,size(I64)=8,size(Bool)=8 since Bool is extended to 64
```
Not really sure how to write this in HolyC :p

### _BIT_FIELD_EXT_U32 (BFieldExtU32)
Signature declared in KernelB.HH `_BIT_FIELD_EXT_U32 U32 BFieldExtU32(U8* bit_field, I64 bit, I64 size);`  
Code located in KUtils.HC

```asm
_BIT_FIELD_EXT_U32:: ; (U8* bit_field, I64 bit, I64 size) -> U32
	PUSH	RBP ; Save RBP
	MOV	RBP,RSP ; Move stack pointer upwards

	MOV	RBX,U64 SF_ARG2[RBP] ; RBX=bit
	MOV	RCX,RBX ; RCX=bit

	; These four lines basically extract a 64 bit val
	SHR	RBX,3 ; RBX=RBX>>3 = bit >> 3
	ADD	RBX,U64 SF_ARG1[RBP] ; RBX += bit_field
	; bit_field is a pointer, so this makes RBX hold a pointer to bit_field+(bit >> 3)
	MOV	RDX,U64 [RBX] ; RDX=*RBX; Set RDX to the value held at RBX.

	; Turn the 64 bit val into a 32 bit (in a 64bit val)
	AND	CL,7 ; CL = CL & 7. CL is lowest eight bits of RCX (which we earlier set to bit)
	SHR	RDX,CL ; RDX = RDX >> CL
	; So above two basically do RDX = RDX >> (CL & 7)

	MOV	RCX,U64 SF_ARG3[RBP] ; RCX=size
	MOV	RAX,1
	SHL	RAX,CL ; RAX = RAX << CL. Since we kow that RAX=1, this is RAX = 1 << CL
	; and CL is lowest eight bits of RCX
	DEC	RAX ; decrement instruction, RAX--
	AND	RAX,RDX ; RAX = RAX & RDX
	; Above several lines is basically: RAX = ((1 << CL) - 1) & RDX

	POP	RBP ; Restore RBP
	RET1	24 ; release bit_field, bit, and size
```

This code is a bit messy since i'm trying to more literally translate it.
```C
U32 _BIT_FIELD_EXT_U32 (U8* bit_field, I64 bit, I64 size) {
	U64 rbx = bit;
	I64 rcx = bit;

	rbx = rbx >> 3;
	rbx += bit_field;

	U64 rdx = *rbx;

	rcx = (rcx & 0xffffffffffffff00) | (rcx & 7); // AND CL,7
	rdx = rdx >> (rcx & 0x00000000000000ff); // SHR RDX,CL

	rcx = size;
	U64 rax = 1;
	rax = rax << (rcx & 0x00000000000000ff); // SHL RAX,CL
	rax--;
	rax = rax & rdx;

	return rax; // i think
}
```
Making it cleaner:
```C
U32 _BIT_FIELD_EXT_U32 (U8* bit_field, I64 bit, I64 size) {
	U64 rdx = (*(bit_field + (bit >> 3))) >> (((bit & 0xffffffffffffff00) | (bit & 7)) & 0x00000000000000ff);
	U64 rax = ((1 << (size & 0x00000000000000ff)) - 1) & rdx;
	return rax;
}
```
''Cleaner'':
```C
U32 _BIT_FIELD_EXT_U32 (U8* field, I64 bit, I64 size) {
	return ((1 << (size & 0x00000000000000ff)) - 1) & (*(bit_field + (bit >> 3))) >> (((bit & 0xffffffffffffff00) | (bit & 7)) & 0x00000000000000ff))
}
```

### _EXP (Exp)
Signature declared in KernelB.HH, `_EXP F64 Exp(F64 d);`  
Code located in KMathA.HC  
TODO: figure this out. I barely know the Float operations.
```asm
_EXP:: ; (F64 d) -> F64
	PUSH	RBP ; Save RBP

	MOV	RBP,RSP

	SUB	RSP,16 ; RSP-=16, so move up by 16 bytes

	FSTCW	U16 -8[RBP] ; ?

	MOV	AX,U16 -8[RBP]
	MOV	U16 -6[RBP],AX
	OR	AX,0x0C00
	MOV	U16 -8[RBP],AX
	FLDCW	U16 -8[RBP]

	FLDL2E
	FLD	U64 SF_ARG1[RBP]
	FMULP	ST1,ST0
	FST	U64 SF_ARG1[RBP]
	FRNDINT
	FLD	U64 SF_ARG1[RBP]
	FSUBRP	ST1,ST0
	F2XM1
	FLD1
	FADDP	ST1,ST0
	FSTP	U64 -16[RBP]
	FLD	U64 SF_ARG1[RBP]
	FLD	U64 -16[RBP]
	FSCALE	// ST0<<ST1
	FSTP	U64 -16[RBP]
	FFREE	ST0
	FINCSTP

	FLDCW	U16 -6[RBP]
	MOV	RAX,U64 -16[RBP]
	LEAVE

	RET1	8
`


## Various bits of reasoning
Trying to understand some of the instructions, and following through on how they work.  

### CMP
CMP subtracts the second op from the first.
CMP RAX, RCX
v = RAX - RCX
SF = v < 0
OF = set if signed arithmetic result in a val too large for it to contain.
So, if, RAX - RCX would result in greater than MAX_64 or smaller than MIN_64, then the OF flag will be set.

### CMOVL
CMP RAX, RCX
CMOVL RAX, RCX
So, if we do:
	RAX=10, RCX=5
	RAX-RCX=5
	SF is 0
	OF is 0
	SF == OF, thus
	CMOVL RAX, RCX does not do anything

If:
	RAX=5, RCX=10
	RAX-RCX=-5
	SF is 1
	OF is 0
	SF != OF
	CMOVL RAX, RCX moves RCX into RAX

If:
	RAX=MIN_64, RCX=1
	RAX-RCX=MAX_64 (I think? It will overflow, at least)
	SF is 1 ? I'm unsure
	OF is 1