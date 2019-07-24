TODO: MAlloc, MAllocIdent, Free, blah, blah, blah.

## Bit Manipulation
Of course you can use the C `&`, `|`, `^`, etc, but there is also some various other functions which you can use. According to the TOS docs, Bit operations are atomic. If you're wanting to use them on multiple cores then use the locked forms.

### Get Set Bit Index - KernelB.HH (Inserted By Compiler? OptPass789A.HC)
`IC_BSF I64 Bsf(I64 bit_field_val)`  
Wrapper around: https://www.felixcloutier.com/x86/bsf instruction  
Returns the index of the first bit (Least-Significant) set (Zero-indexed), returns -1 if it can't find one.  
Example:  
`Bsf(0);` -> `-1`  
`Bsf(0b001);` -> `0`  
`Bsf(0b100);` -> `2`  
`Bsf(0b101);` -> `0`  
Scans from the right to the left.  
### Get Set Bit Index Reverse - KernelB.HH (Inserted By Compiler? OptPass789A.HC)
`IC_BSR I64 Bsr (I64 bit_field_val);`  
Wrapper around: https://www.felixcloutier.com/x86/bsr instruction  
Starts in the reverse direction from `Bsf`, but still returns position from Least-Significant-Bit.  
Example:  
`Bsr(0);` -> `-1`  
`Bsr(0b001);` -> `0`  
`Bsr(0b100);` -> `2`  
`Bsr(0b101);` -> `2`  
Scans from the left to right.
### Bit Test - KernelB.HH
`IC_BT Bool Bt(U8 *bit_field, I64 bit);`  
Wrapper around: https://www.felixcloutier.com/x86/bt instruction  
Returns specified bit.
`U8 testv = 0b100;`  
`Bt(&testv, 0);` -> `0`  
`Bt(&testv, 1);` -> `0`  
`Bt(&testv, 2);` -> `1`

### Bit test and complement - KernelB.HH
`IC_BTC Bool Btc (U8 *bit_field, I64 bit);`  
Wrapper around https://www.felixcloutier.com/x86/btc instruction  
Returns specified bit, and xors it with 1.  

### Bit test and reset to zero - KernelB.HH
`IC_BTR Bool Btr (U8 *bit_field, I64 bit);`  
Wrapper around https://www.felixcloutier.com/x86/btr instruction  
Returns specified bit value, and sets it to 0.  

### Bit test and set to one - KernelB.HH
`IC_BTS Bool Bts (U8 *bit_field, I64 bit);`  
Wrapper around https://www.felixcloutier.com/x86/bts instruction  
Returns specified bit value, and sets it to 1.  

### Locked Bit Test and complement - KernelB.HH
`IC_LBTC Bool LBtc (U8 *bit_field, I64 bit);`  
Wrapper around https://www.felixcloutier.com/x86/btc locked version instruction
(See: `Btc`)

### Locked bit test and reset to zero - KernelB.HH
`IC_LBTS Bool LBtr (U8 *bit_field, I64 bit);`  
Wrapper around https://www.felixcloutier.com/x86/btr locked version instruction
See Btr.  

### Locked bit test and set to one - KernelB.HH
`IC_LBTS Bool LBts (U8 *bit_field, I64 bit);`  
Wrapper around https://www.felixcloutier.com/x86/bts locked version instruction
See Bts.  

### Set Bit To value - KernelB.HH (, KUtils.HC _BEQU)
`_BEQU Bool BEqu (U8 *bit_field, I64 bit, Bool val);`  
Sets a `bit` in the `bit_field` equal to `val`.

### Extract U32
`_BIT_FIELD_EXT_U32 U32 BFieldExtU32 (U8 *bit_field, I64 bit, I64 size);`  
TODO: test

### Insert U32
`_BIT_FIELD_OR_U32 U32 BFieldOrU32 (U8 *bit_field, I64 bit, I64 size);`  
TODO: test

### Locked Set Bit
`_LBEQU Bool LBEqu (U8 *bit_field, I64 bit, Bool val);`
