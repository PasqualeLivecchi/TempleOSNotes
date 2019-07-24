# Various Defines

```C
#define I8_MIN (-0x80)
#define I8_MAX 0x7F
#define U8_MIN 0
#define U8_MAX 0xFF
#define I16_MIN (-0x8000)
#define I16_MAX 0x7FFF
#define U16_MIN 0
#define U16_MAX 0xFFFF
#define I32_MIN (-0x80000000)
#define I32_MAX 0x7FFFFFFF
#define U32_MIN 0
#define U32_MAX 0xFFFFFFFF
#define I64_MIN (-0x8000000000000000)
#define I64_MAX 0x7FFFFFFFFFFFFFFF
#define U64_MIN 0
#define U64_MAX 0xFFFFFFFFFFFFFFFF
#define INVALID_PTR	I64_MAX
// ..

#define U64_F64_MAX (0x43F0000000000000(F64))
#define F64_MAX (0x7FEFFFFFFFFFFFFF(F64))
#define F64_MIN (0xFFEFFFFFFFFFFFFF(F64))
#define inf	(0x7FF0000000000000(F64))
#define ì	(0x7FF0000000000000(F64))
#define pi	(0x400921FB54442D18(F64))
#define ã	(0x400921FB54442D18(F64))
#define exp_1	(0x4005BF0A8B145769(F64)) //The number "e"
#define log2_10 (0x400A934F0979A371(F64))
#define log2_e	(0x3FF71547652B82FE(F64))
#define log10_2	(0x3FD34413509F79FF(F64))
#define loge_2	(0x3FE62E42FEFA39EF(F64))
#define sqrt2	(0x3FF6A09E667F3BCD(F64))
#define eps	(0x3CB0000000000000(F64))
```  

# Integer Unions
  
Numbers are unions of smaller types (the `U8` seemingly being the smallest.), which are two signed/unsigned values (of the smaller type). So a `U16` (and `I16`, their declaration is equivalent) has two properties, `i8` which is an `I8i[2]` (which is just an `I8`); and `u8` which is an `U8i[2]`.  
You can use this to access the upper and lower values.  
[Declarations of U16i/I16i](https://github.com/minexew/TempleOS/blob/1dd8859b7803355f41d75222d01ed42d5dda057f/Kernel/KernelA.HH#L67-L77):  
```C
U16i union U16
{
  I8i	i8[2];
  U8i	u8[2];
};

I16i union I16
{
  I8i	i8[2];
  U8i	u8[2];
};
```

The higher value integers do the same, but increase the size respectively.
`U32` (and `I32`) are declared as:  
```C
{
  I8i	i8[4];
  U8i	u8[4];
  I16	i16[2];
  U16	u16[2];
};
```
Which, since it's a union means you can read the varying byte places as you wish. This same style goes to I64.  
  
TODO: add note I remember that Terry wrote which I remember being related to this.  

# General Functions
## Absolute F64 - KernelB.HH (Inserted By Compiler From Templates? OptPass789A.HC)
`IC_ABS F64 Abs(F64 d);`  
Returns absolute number. |d|. It's distance from 0.  
## Absolute I64 - KernelB.HH (Inserted By Compiler? OptPass789A.HC)
`IC_ABS_I64 I64 AbsI64 (I64 i);`  
Same as `Abs` but for I64. If you want to use this on small sized ints then you'll need to convert it higher.  
If you want to use this on unsigned integers, you should probably get help.  
## Max of two I64s - KernelB.HH (From ICMinMax OptPass789A.HC)
`IC_MAX_I64 I64 MaxI64 (I64 n1, I64 n2);`
## Max of two U64s - KernelB.HH (From ICMinMax OptPass789A.HC)
`IC_MAX_U64 U64 MaxU64 (U64 n1, U64 n2);`
## Min of two I64s - KernelB.HH (From ICMinMax OptPass789A.HC)
`IC_MIN_I64 I64 MinI64 (I64 n1, I64 n2);`
## Min of two U64s - KernelB.HH (From ICMinMax OptPass789A.HC)
`IC_MIN_U64 U64 MinU64 (U64 n1, U64 n2);`
## Modulo of two U64s - KernelB.HH (From ICModU64 OptPass789A.HC)
`IC_MOD_U64 U64 ModU64 (U64 *q, U64 d);`
## Get Sign Of I64 - KernelB.HH (Partially from template? OptPass789A.HC)
`IC_SIGN_I64 I64 SignI64 (I64 i);`
Returns the sign of the number. `-1`, `0`, or `1`.  
I presume that -1 is negative number, 0 is 0 (due to not being positive or negative), and 1 is positive.  
(TODO: check this statement)  
## Get Sign of F64 - KernelB.HH (, KMathA.HC _SIGN)
`_SIGN F64 Sign(F64 d);`
## Square of F64 - KernelB.HH (From Template? OptPass789A.HC)
`IC_SQR F64 Sqr (F64 d);`  
d raised to the second power. d*d.  
## Square of I64 - KernelB.HH (From ICSqr. OptPass789A.HC)
`IC_SQR_I64 I64 SqrI64(I64 i);`  
## Square of U64 - KernelB.HH (From ICSqr. OptPass789A.HC)
`IC_SQR_U64 U64 SqrU64(U64 i);`
## Square Root Of F64 - KernelB.HH ()
`IC_SQRT F64 Sqrt(F64 d);`
## Swap I64 Values - KernelB.HH ()
`IC_SWAP_I64 U0 SwapI64 (I64 *n1, I64 *n2);`
Swaps the values of two I64's. n1 becomes n2, and n2 becomes what n1 was.
Not atomic.
## Swap U16 Values - KernelB.HH (From ICSwap. OptPass789A.HC)
`IC_SWAP_U16 U0 SwapU16 (U16 *n1, U16 *n2);`
Not atomic.
## Swap U32 Values - KernelB.HH (From ICSwap. OptPass789A.HC)
`IC_SWAP_U32 U0 SwapU32 (U32 *n1, U32 *n2);`
Not atomic.
## Swap U8 Values - KernelB.HH (From ICSwap. OptPass789A.HC)
`IC_SWAP_U8 U0 SwapU8 (U8* n1, U8* n2);`
Not atomic.
There doesn't seem to be an I32, I16, or I8 version. I imagine you might be able to just use these with those values and just convert them back (but you should test that first), or, just write the simple function if you need it.
## Ceiling of F64 - KernelB.HH ()
`_CEIL F64 Ceil (F64 d);`
## Clamp I64 - KernelB.HH (, KUtils.HC _CLAMP_I64)
`_CLAMP_I64 I64 ClampI64 (I64 num, I64 lo, I64 hi);`  
`// Clamp to I64 [] range`  
Defined basically as (See FiguringOutAsm file for the asm, and why I think this):  
```C
U64 ClampI64 (I64 num, I64 lo, I64 hi) {
	if (num < lo) {
		num = lo;
	}
	if (num > hi) {
		num = hi;
	}
	return num;
}
```
(Note: this does mean that `ClampI64(5, 7, 4)` will output `4`.)  
## Clamp U64 - KernelB.HH (, KUtils.HC _CLAMP_U64)
`_CLAMP_U64 U64 ClampU64 (U64 num, U64 lo, U64 hi);`  
`// Clamp to U64 [] range`  
I assume it works the same as Clamp I64. (TODO: test it)  
## Exponential Function F64 - KernelB.HH (, KMathA.HC _EXP)
`_EXP F64 Exp(F64 d);`  
returns e^d.  
## Floor F64 - KernelB.HH (, KMathA.HC _FLOOR)
`_FLOOR F64 Floor (F64 d);`  
Somewhat obviously, it floors the number you pass to it.
## Logarithm - KernelB.HH (, KMathA.HC)
`_LN F64 Ln (F64 d);`
## Logarithm (Base 10) - KernelB.HH (, KMathA.HC)
`_LOG10 F64 Log10(F64 d);`
## Logarithm (Base 2) - KernelB.HH (, KMathA.HC)
`_LOG2 F64 Log2 (F64 d);`
## Pow (Exponents) - KernelB.HH (, KMathA.HC)
`_POW F64 Pow (F64 base, F64 power);`  
returns base^power  
## Pow 10 - KernelB.HH (, KMathA.HC)
`_POW10 F64 Pow10 (F64 d);`  
returns d^10  
Much shorter assembly than SYS_POW.
## Round F64 - KernelB.HH (, KMathA.HC)
`_ROUND F64 Round (F64 d);`
Rounds `d` to a whole number.
## Truncate F64 - KernelB.HH (, KMathA.HC _TRUNC)
`_TRUNC F64 Trunc(F64 d);`


# Trigonometry
## Rect To Polar - AMath.HC
`U0 R2P (F64 *_max=NULL, F64 *_arg=NULL, F64 x, F64 y);`  
`// Returns angle in range (-π,π]`  
Ignores `_max` and `_arg` if they are `NULL`.  
## Polar To Rect - AMath.HC
`U0 P2R (F64 *_x=NULL, F64 *_y=NULL, F64 mag, F64 arg);`  
Ignores `_x` and `_y` if they are `NULL`.  
## Get Polar Coordinate Angle - KernelB.HH (?)
`_ARG F64 Arg (F64 x, F64 y);`
## Wrap - AMath.HC
`F64 Wrap (F64 θ, F64 base=-π);`  
`// Returns angle in range [base, base+2*π]`  
Yes, the first parameter is theta (θ) in the code. Not that it really matters.  
## Distance squared between two points - AMath.HC
`I64 DistSqrI64 (I64 x1, I64 y1, I64 x2, I64 y2);`  
(Note that if you want the distance between two points from this you'll have to square-root it.)  
## Sine - KernelB.HH (From Template? OptPass789A.HC)
`IC_SIN F64 Sin (F64 d);`
## Arc Sine (Inverse Sin) - AMath.HC
`F64 ASin (F64 s);`  
## Arc Cosine (Inverse Cosine) - AMath.HC
`F64 ACos(F64 c);`  
## Sinh (Hyperbolic Sine) - AMath.HC
`F64 Sinh(F64 x);`  
## Cosh (Hyperbolic Cosine) - AMath.HC
`F64 Cosh(F64 x);`  
## Arc Tangent (Inverse Tangent) - KernelB.HH (KMathA.HC)
`IC_ATAN F64 ATan (F64 d);`
## Cosine - KernelB.HH - (From Templates. OptPass789A.HC)
`IC_COS F64 Cos (F64 d);`
## Tangent - KernelB.HH (From Templates. OptPass789A.HC)
`IC_TAN F64 Tan(F64 d);`




# Complex
A simple class which has two `F64` properties: `x` and `y`.  Used to deal with complex numbers. Declared in: (TODO: the file)  
Note that alot (read: all) return a pointer to a `Complex` instance, but they do not allocate it themselves. They're simply returning the point you passed them which holds  all the changes it did.  
## Add Two Complexes - AMath.HC.Z
`Complex *CAdd (Complex *sum, Complex *n1, Complex *n2);`  
sum = n1 + n2  
Adds the two Complex's properties, setting sums values to it. (It does not include sum in the calculations. So you **WON'T** end up with sum=n1+n2+sum)  
## Subtract Two Complexes - AMath.HC.Z
`Complex *CSub (Complex *diff, Complex *n1, Complex *n2);`  
diff = n1 - n2  
Does not include diff in the calculations.  
## Multiply Two Complexes - AMath.HC.Z
`Complex *CMul (Complex *prod, Complex *n1, Complex *n2);`  
prod = n1 * n2  
## Divide Two Complexes - AMath.HC.Z
`Complex *CDiv (Complex *quot, Complex *n1, Complex *n2);`
quot = n1 / n2  
Note that this does 'really' do n1/n2. It does, what I assume to be, division of complex numbers. If you want normal division (perhaps you are using this as a point class, though I'd rec writing your own.), then don't use this.  

## Scale Two Complexes - AMath.HC.Z
`Complex *CScale (Complex *dst, F64 s);`  
dst *= s  

## Copy Complex - AMath.HC.Z
`Complex *CCopy (Complex *dst, Complex *src);`  
dst = src  
Simply copies over the properties.  

## Set Complex XY - AMath.HC.Z
`Complex *CEqu (Complex *dst, F64 x, F64 y);`  
dst = (x,y)  
Simply sets the x and y properties.  

## Evaluate Complex Polynomial
`Complex *CPoly (Complex *dst, I64 n, Complex *zeros, Complex *x);`
Eval a complex polynomial. Honestly, I have no cue what it's doing.


# Other
These are various strange ones which are kindof math.  
## Clear Floating point exception flags - KernelB.HH (, KMathA.HC)
`_FCLEX U0 Fclex();`  
Literally a straight call to the [FCLEX](https://www.felixcloutier.com/x86/fclex:fnclex) x86 instruction.
## Load x87 FPU Control Word - KernelB.HH (, KMathA.HC)
`_FLDCW U0 Fldcw(U16 w);`  
Basically just passes the argument in to the [FLDCW](https://www.felixcloutier.com/x86/fldcw) x86 instruction.
## Store x87 FPU Control Word - KernelB.HH (, KMathA.HC _FSTCW)
`_FSTCW U16 Fstcw();`  
[FSTCW](https://www.felixcloutier.com/x86/fstcw:fnstcw)
## Store x87 FPU Status Word - KernelB.HH (, KMathA.HC _FSTSW)
`_FSTSW U16 Fstsw();`  
[FSTSW](https://www.felixcloutier.com/x86/fstsw:fnstsw)