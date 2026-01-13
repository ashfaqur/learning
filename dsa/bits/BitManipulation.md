# Bit Manipulation

# Table

+----------------+---------+-----+
|  Binary (4-bit)| Decimal | Hex |
+----------------+---------+-----+
|      0000      |    0    |  0  |
|      0001      |    1    |  1  |
|      0010      |    2    |  2  |
|      0011      |    3    |  3  |
|      0100      |    4    |  4  |
|      0101      |    5    |  5  |
|      0110      |    6    |  6  |
|      0111      |    7    |  7  |
|      1000      |    8    |  8  |
|      1001      |    9    |  9  |
|      1010      |   10    |  A  |
|      1011      |   11    |  B  |
|      1100      |   12    |  C  |
|      1101      |   13    |  D  |
|      1110      |   14    |  E  |
|      1111      |   15    |  F  |
+----------------+---------+-----+

# Bitwise Operators in Java

```java
+----------+----------------------+----------------------------------------------+
| Operator | Name                 | Description / Example                        |
+----------+----------------------+----------------------------------------------+
| &        | AND                  | Bitwise AND: 1 if both bits are 1, else 0. |
|          |                      | Example: 1101 & 1011 = 1001                 |
+----------+----------------------+----------------------------------------------+
| |        | OR                   | Bitwise OR: 1 if at least one bit is 1.     |
|          |                      | Example: 1101 | 1011 = 1111                 |
+----------+----------------------+----------------------------------------------+
| ^        | XOR                  | Bitwise exclusive OR: 1 if bits differ.     |
|          |                      | Example: 1101 ^ 1011 = 0110                 |
+----------+----------------------+----------------------------------------------+
| ~        | NOT                  | Bitwise negation: flips all bits.           |
|          |                      | Example (4-bit): ~1101 = 0010               |
+----------+----------------------+----------------------------------------------+
| <<       | Left shift           | Shift bits left, fill 0 on right.           |
|          |                      | Multiply by 2^N. Example: 0011 << 2 = 1100 |
+----------+----------------------+----------------------------------------------+
| >>       | Signed right shift   | Shift bits right, fill with sign bit.       |
|          |                      | Divide by 2^N, remainder dropped.           |
|          |                      | Example: 1101 >> 2 = 0011                   |
+----------+----------------------+----------------------------------------------+
| >>>      | Unsigned right shift | Shift bits right, fill 0 on left.           |
|          |                      | Example: 1101 >>> 2 = 0011                  |
+----------+----------------------+----------------------------------------------+
```

Quick Notes / Tricks

```java
&   → clear / mask bits
|   → set bits
^   → toggle bits
~   → flip all bits (two’s complement if signed)
<<  → multiply by powers of 2
>>  → divide by powers of 2 (integer division, sign preserved)
>>> → divide by powers of 2 (unsigned, fills left with 0)
```

# Binary Addition

Binary works like decimal, but with base 2.

```java
Example:
  0110
+ 0110
------
  1100

Because:
0+0=0
1+1=10 → write 0, carry 1
1+1+1=11 → write 1, carry 1
0+0+1=1
```

# Binary Subtraction

Rules:
0 - 0 = 0
1 - 0 = 1
1 - 1 = 0
0 - 1 = borrow 1 from the next higher bit → 10₂ - 1 = 1

Method:
1. Subtract bit by bit from right to left (LSB → MSB)
2. If current bit < subtrahend bit, borrow from the next available 1 on the left
3. Each borrow adds 2 (10₂) to the current column
4. Continue until all bits are processed

```java
Example:  1000₂ - 0110₂  (8 - 6)

   1 0 0 0
 - 0 1 1 0
-------------
   0 0 1 0  (2 decimal)

Check: 8 - 6 = 2 ✅
```


# Binary Multiplication (Manual)

Rules:
0 × 0 = 0
0 × 1 = 0
1 × 0 = 0
1 × 1 = 1

Method:
1. Multiply each bit of the bottom number by the top number (0 → 0, 1 → top number)
2. Shift each row left according to its position (like decimal multiplication)
3. Add all rows using binary addition

```java
Example:  0011 × 0101  (3 × 5)

      0011   (3)
×     0101   (5)
----------------
      0011   ← 1 × 0011
     0000    ← 0 × 0011 (shifted)
    0011     ← 1 × 0011 (shifted two positions)
   0000      ← 0 × 0011 (shifted three positions)
----------------
   1111      (15 decimal)

Check: 3 × 5 = 15 ✅
```

# Binary Multiplication and Shifts

```java
Left shift:
x << 1 = x × 2
x << 2 = x × 4
x << 3 = x × 8
...
Left shift by N multiplies by 2^N.

Right shift (unsigned):
x >> 1 = x ÷ 2  (drops remainder)

Binary multiplication:
Multiply like decimal long multiplication, but only with 0 and 1.

Rules:
x × 0 = 0
x × 1 = x (shifted)

Method:
For each 1-bit in the multiplier:
- Shift the number by that bit’s position
- Add the shifted values

Example:
  0110 (6)
× 1011 (11)

1011 = 8 + 2 + 1
So:
6 × 11 = (6 << 3) + (6 << 1) + 6

In binary:
0110 << 3 = 0110000
0110 << 1 =   01100
0110      =    0110
------------------
           1000010 (66)
```

# Binary Right Shift (>>)

- Right shift by N bits divides the number by 2^N (integer division)
- Any remainder is discarded (fraction lost)
- Example:
```java
1101₂ >> 1 = 0110₂   (13 ÷ 2 = 6, remainder 1 lost)
1101₂ >> 2 = 0011₂   (13 ÷ 4 = 3, remainder 1 lost)
```
Rule of thumb:
x >> N  =  x ÷ 2^N (integer division, drops remainder)

# Binary Bitwise Operations


## XOR (^):

```java
- 0 ^ 0 = 0
- 0 ^ 1 = 1
- 1 ^ 0 = 1
- 1 ^ 1 = 0

Example:
1101 ^ 0101 = 1000
```

## NOT (~):

```java
- Flip all bits: 0 → 1, 1 → 0
Example (4-bit): ~1101 = 0010
```

# XOR with negation gives all 1s:

A ^ ~A = 111...111  (all bits 1)

- Every bit: 0 ^ 1 = 1, 1 ^ 0 = 1
- Works for any bit-width
- Example (4-bit):
  1010 ^ 0101 = 1111

# Clearing bits with a mask:

number & (~0 << N)

- ~0 = all 1s
- << N = shift left N positions → lower N bits become 0
- & = keep bits where mask has 1, clear bits where mask has 0

Example (4-bit):
1011 & (~0 << 2)
~0 = 1111
~0 << 2 = 1100
1011 & 1100 = 1000

# Bit Facts and Tricks

## XOR (^)
- `x ^ 0 = x` → XOR with 0 does nothing  
- `x ^ 1 = ~x` → XOR with 1 flips the bit  
- `x ^ x = 0` → a bit cancels itself  

## AND (&)
- `x & 0 = 0` → AND with 0 clears the bit  
- `x & 1 = x` → AND with 1 keeps the bit  
- `x & x = x`

## OR (|)
- `x | 0 = x` → OR with 0 does nothing  
- `x | 1 = 1` → OR with 1 forces the bit to 1  
- `x | x = x`
