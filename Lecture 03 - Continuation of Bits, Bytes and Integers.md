# Lecture 03 - Continuation of Bits, Bytes, and Integers

### Addition
- Unsigned
    -  s = UAdd(u, v) = (u + v)mod2^w^
```
  1 1 0 1 (13)
+ 0 1 0 1 (5)
----------
1 0 0 1 0 (2)

// Ignore the first bit as number of bits are fixed. Result of 13 + 5 = 2 for a 4 bits addition due to integer overflow. Same result can also be derived from (13 + 5)mod(2^4) = 2.
```
- Two's complement
    - s = U2T(UAdd(T2U(x), T2U(y)))
    - TAdd and UAdd have identical bit-level behavior (see examples below)
```
// Ex1: No integer overflow, result is same as unsigned using the same binaries for addition

  1 1 0 1 (-3)
+ 0 1 0 1 (5)
----------
1 0 0 1 0 (2)

// Ex2: With integer overflow (Negative overflow)

  1 1 0 1 (-3)
+ 1 0 1 0 (-6)
----------
1 0 1 1 1 (7)

// Ex3: With integer overflow (Positive overflow)

  0 1 1 1 (7)
+ 0 1 0 1 (5)
----------
  1 1 0 0 (-4)
```

### Multiplication
- Unsigned
    - UMult(u, v) = (u * v)mod2^w^
    - For a 4-bit integer, 5 * 5 = 9 (25%16)
```
// Create a binary with 2*w number of bits (4 -> 8)
<!---
Ex1: 5*5 = 25, which can be represented in an 2*4= 8-bits integer

0 0 0 1 1 0 0 1 (25)

Ignore the first w bits and the remaining bits would be the result of the unsigned multiplication:

1 0 0 1 (9)
-->
```
- Signed
    - Tmult(u, v) = U2T((u*v)mod2^w^)
    - Ignore high order w bits
```
// Create a binary with 2*w number of bits (4 -> 8)

<!---
Ex1: 5*4 = 20, which can be represented in an 2*4= 8-bits integer

0 0 0 1 0 1 0 0 (20)

Ignore the first w bits and the remaining bits would be the result of the signed multiplication:

0 1 0 0 (4)

-->
<!---
Ex2: 5*5 = 25, which can be represented in an 2*4= 8-bits integer

0 0 0 1 1 0 0 1 (25)

Ignore the first w bits and the remaining bits would be the result of the signed multiplication:

1 0 0 1 (-7)
-->
<!---
Ex2: -3*-2 = 13*14 = 182 in unsigned representation, which can be represented in an 2*4= 8-bits integer

1 0 1 1 0 1 1 0 (182)

Ignore the first w bits and the remaining bits would be the result of the signed multiplication:

0 1 1 0 (6)
-->
```

- Power-of-2 Multiplication with Shift
    - u << k gives u * 2^k^
    - Both signed and unsigned
    - Shift instructions are used instead of multiplication instructions in the compiler for Power-of-2 multiplications as it takes less clock cycles to do a shift operation
```
<!---
Ex1 (Unsigned): 5*4 = 5*(2^2) = 20. Just left shift the integer by 2 bits

0 1 0 1 (5) -> 0 1 0 1 0 0 (20)

Throw away the extra bits on the left and the result is 4

0 1 0 1 0 0 (20) --> 0 1 0 0 (4)
-->
<!---
Ex2 (Signed): 7*4 = 7*(2^2) = 28. Just left shift the integer by 2 bits

0 1 1 1 (7) -> 0 1 1 1 0 0 (28)

Throw away the extra bits on the left and the result is 4

0 1 1 1 0 0 (28) --> 1 1 0 0 (-4)
-->
```

### Division

- Power-of-2 Division with Shift
    - Unsigned: Logical right shift by k bits where k represents the power in 2^k^ (Fill with 0's on left)
    - Signed: Add a bias (Addition of 1 bit) and Arithmetic right shift by k bits where k represents the power in 2^k^ (Replicate most significant bit on left)
```
<!---
Ex1: Unsigned Power-of-2 division
0 1 1 0 (6) //divide by 2
0 0 1 1 (3) //divide by 2
0 0 0 1 (1)
-->
<!---
Ex1: Signed Power-of-2 division
1 0 1 0 (6) //add a bias
1 0 1 1     //divide by 2
1 1 0 1 (-3) //add a bias
1 1 1 0     // divide by 2
1 1 1 1 (-1)
-->
```

### Trick to negate a number quickly
```
  1 0 1 0 (-6)
  0 1 0 1 (After flipping the bits)
+ 0 0 0 1 (Add 1 to the flipped integer)
---------------
  0 1 1 0 (6)
```

### Counting down with Unsigned
```
unsigned i;
for (i = cnt-2; i < cnt; i--) {
    a[i] += a[i+1];
}

//Even better using size_t
size_t i
for (i = cnt -2; i < cnt; i--) {
    a[i] += a[i+1];
}
```

### Byte Ordering
- Little Endian: Least significant byte has lowest address. Common in most Intel-compatible machines.

- Big Endian: Least significant byte has highest address. Common in machines from IBM and Oracle.

```
// Variable x has 4-byte of 0x01234567

// Big Endian
0x100|0x101|0x102|0x103|
01   |23   |45   |67   |

// Little Endian
0x100|0x101|0x102|0x103|
67   |45   |23   |01   |
```
- Representing Strings: Array of characters with a final character of 0. All machines have the same order.
```
char S[6] = "18213";

0x31 | 0x38 | 0x32 | 0x31 | 0x33 | 0x00
```

### C Puzzles
```
int x,y;
unsigned ux, uy;
ux = x;
uy = y;

x < 0 -> ((x*2) < 0)        [False statement, consider TMin]
ux >= 0                     [True Statement]
x & 7 == 7 -> (x << 30) < 0 [True Statement]
//Last 3 bits are 1 1 1, left shift by 30 bits would result in 1 1 ... which is negative
ux > -1                     [False Statement]
x > y -> -x < -y            [False Statement, consider TMin]
//Tmin flipped is still Tmin which stays negative
x * x >= 0                  [False Statement, consider overflow]
x >= 0 -> -x <= 0           [True statement, all +ve have inverse]
x <= 0 -> -x >= 0           [False Statement, consider TMin]
(x|-x) >> 31 == -1          [False Statement, only False for 0]
                             # Note: Signed int has arithmetic shift
```