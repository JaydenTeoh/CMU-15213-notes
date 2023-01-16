# Lecture 02 - Bits, Bytes, and Integers

### Everything is bits
- Each bit is 0 or 1
- By encoding/intepreting sets of bits in various ways
    - computers determine what to do
- Why bits?
    - Easy to store with bistable elements
    - Reliably transmitted on noisy and inaccurate wires

### Example: Representing & Manipulating Sets
- Representation
    - Width w bit vector represents subsets of {0, ..., w-1}
    ```
    01101001 // {0, 3, 5, 6}
    76543210
    
    01010101 // {0, 2, 4, 6}
    76543210
    ```
- Operations
    - Intersection (&)
    `01000001 //{0, 6}`
    - Union (|)
    `01111101 //{0, 2, 3, 4, 5, 6}`
    - Symmetric difference (^)
    `00111100 //{2, 3, 4, 5}`
    - Complement (~)
    `10101010 //{1, 3, 5, 7}`
- Examples (char data type)
    - ~0x41 -> 0xBE
        - ~01000001 -> 10111110
    - ~0x00 -> 0xFF
        - ~00000000 -> 11111111
    - ~0x69 & 0x55 -> 0x41
        - 01101001 & 01010101 -> 01000001
    - ~0x69 | 0x55 -> 0x7D
        - ~01101001 | 01010101 -> 01111101

### Contrast: Logic Operations in C
- Contrast to Logical Operators
    - &&, ||, !
        - View 0 as "False"
        - Anything nonzero as "True"
        - Always return 0 or 1
        - Early termination (test if it's a null pointer before accessing the value)
- Examples (char data type)
    - !0x41 -> 0x00
    - !0x00 -> 0x01
    - !!0x41 -> 0x01 (Truthy)
    - 0x69 && 0x55 -> 0x01
    - 0x69 || 0x55 => 0x01

### Shift Operations
- Left Shift: x << y
    - Shift bit-vector **x** left **y** positions
        - Throw away extra bits on left
        - Fill with 0's on right
- Right Shift: x >> y
    - Shift bit-vector **x** right **y** positions
        - Throw away extra bits on right
    - Logical shift
        - Fill with 0's on left
    - Arithmetic shift
        - Replicate most significant bit on left
- Undefined Behavior
    - Shift amount < 0 or >= word size
    
Argument x | 01100010
------ | -------
<< 3 | 00010000
Log. >> 2 | 00011000
Arith. >> 2 | 00011000

Argument y | 10100010
------ | -------
<< 3 | 00010000
Log. >> 2 | 00101000
Arith. >> 2 | 11101000

### Unsigned & Signed Numeric Values
- Unsigned values: The most left bit is 1 and bit positions are multiplied with the corresponding powers of 2
    ```
    //8 bit number
    1 1 1 1 1 1 1 1 1
    128 64 32 16 8 4 2 1
    ----------------------
    128 + 64 + 32 + 16 + 8 + 4 + 2 + 1
                            = 255
                            = 2^8 -1
    ```
- Signed / Two's complement values: The most left bit is negative and bit positions are multiplied with its corresponding power of 2
     ```
    //8 bit number
    -1 0 0 0 0 0 0 0 0
    128 64 32 16 8 4 2 1
    ----------------------
                        = -128
                        = -(2^(8-1))
                        
    0 1 1 1 1 1 1 1
    128 64 32 16 8 4 2 1
    ----------------------
    64 + 32 + 16 + 8 + 4 + 2 + 1
                        = 127
                        = 2^(8-1) - 1
    ```
- Difference between Umax (Unsigned max) and Tmax (Two's complement max) would be the 2^(num of bits - 1)
- A large positive unsigned number will become negative when using signed numbers due to integer overflow

```
// First example
unsigned i;
for (i=n-1; i>=0; i--) {
	func(a[i])
}
<!---
since i is unsigned, it will go back to being Umax after 1 is subtracted from 0. Loop will not end => memory fault
-->

// Second example
int i;
for (i=n-1; i - sizeof(int) >= 0; i--) {
	func(a[i])
} 
<!---
sizeof is an operator which returns an unsigned integer. Arithmetic between signed and unsigned will always be converted to an unsigned result.
-->
```

### Casting surprises
- Expression Evaluation
    - If there is a mix of unsigned and signed in single expression, signed values implicitly cast to unsigned
    - Including comparison operations <, >, ==, <=, >=

Constant 1 | Constant 2 | Relation | Evaluation
------ | ------- | ------ | -------
0 | 0U | == | unsigned
-1 | 0 | < | signed
-1 | 0U | > | unsigned
-1 | -2 | > | signed

### Summary
- Expanding (e.g. short int to int)
    - Unsigned: Zeros added (Logical Right shift)
    - Signed (Two's complement): Sign extension. Replicate the most significant bit on the left (Arithmetic shift)
    ```
    1 1 1 0 -> -8 + 4 + 2 = -2
    1 1 1 1 0 -> -16 + 8 + 4 + 2 = -2
    ```
- Truncating (e.g. unsigned to unsigned short)
    - Unsigned/signed: bits are truncated from the left
    - Unsigned: x(new) = x % 2^k where truncation target is k-bits
    - Signed: similar to mod but can deduct to a negative integer
    
