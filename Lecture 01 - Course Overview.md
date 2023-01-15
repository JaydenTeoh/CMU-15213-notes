# Lecture 01 - Course Overview

### _#1 Ints are not integers, Floats are not Reals_
**Example 1: Is x^2 >= 0**
- Floats: Yes!
- Ints: No!
    ```
    50000 * 50000 = -1794967297 // This is due to integer overflow
    ```
    However, despite integer overflow, integer arithmetic still obeys the commutative and associative properties of mathematics
    ```
    300*400*500*600 = 1640261632
    400*500*600*300 = 1640261632 //Rearranging of the order of the arithmetic does not affect the outcome, even with integer overflow!
    ```

**Example 2: Is (x + y) + z = x + (y + z)**
- Unsigned & Signed Ints: Yes!
- Floats:
    ```
    (1e20 + -1e20) + 3.14 = 3.14
    1e20 + (-1e20 + 3.14) = 0 //Float arithmetic is not associative due to roundoff
    ```
**Takeaways:**
Both number systems use finite representations of things that are potentially infinite in their expense. So there are some compromises in how they work:

1. For ints, there is integer overflow problem where you can run out of room
2. For floats, there is roundoff problem where you drop the digits that aren't significant (there is overflow for floats too!)

We cannot assume "usual" mathematical properties for computer arithmetic due to finiteness of representations. We need to understand these corner cases as they can be important issues for serious application programs.

### _#2 You've Got to Know Assembly
**Understanding assembly is key to machine-level execution model**
- Behaviour of programs in presence of bugs
- Tuning program performance
    - Understand optimizations done/ not done by the compiler
    - Understanding sources of program inefficiency
- Implementing system software
- Creating / fighting malware

### _#3 Memory Matters_
**Random Access Memory is an Unphysical Abstraction**
1. Memory is not unbounded
    - it must be allocated and managed
    - many applications are memory dominated
2. Memory referencing bugs especially pernicious
    - effects are distant in both time and space
3. Memory performance is not uniform
    - cache and virtual memory effects can greatly affect program performance
    - adapting program to characteristics of memory system can lead to major speed improvements
    
**Example 1:**
```
typedef struct {
	int a[2];
	double d;
} struct_t;

double fun(int i) {
	volatile struct_t s;
	s.d = 3.14;
	s.a[i] = 1073741824; /* May be out of bounds */
	return s.d;
}

// fun(0) -> 3.14
// fun(1) -> 3.14
// fun(2) -> 3.139999866
// fun(3) -> 2.000000610
// fun(4) -> 3.14
// fun(6) -> Segmentation Fault
```
- C and C++ do not provide any memory protection
    - allow out of bounds array references
    - invalid pointer values
    - abuses of malloc/free (memory allocation)

### _#4 There's more to performance than asymptotic complexity_
- Constant factors matter too!
- Must optimize at multiple levels (algorithm, data representations, procedures and loops)
- Must understand system to optimize performance
    - how programs compile and execute
    - how to measure program performance and identify bottlenecks
    - how to improve performance without destroying code modularity and generality

**Example 1:**
```
void copyij(int src[2048][2048], int dst[2048][2048]) {
	int i,j;
	for (i=0; i<2048; i++)
		for (j=0; j<2048; j++)
			dst[i][j] = src[i][j]
}

// Runtime: 4 ms

void copyij(int src[2048][2048], int dst[2048][2048]) {
	int i,j;
	for (j=0; j<2048; j++)
		for (i=0; i<2048; i++)
			dst[i][j] = src[i][j]
}

// Runtime: 81 ms
```
- Hierarchical memory organization
- Performance depends on access patterns
    - Including how step through multi-dimensional array

### _#5 Computers do more than execute programs_
- They need to get data in and out
- They communivate with each other over networks