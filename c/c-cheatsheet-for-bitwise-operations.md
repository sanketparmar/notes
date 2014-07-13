Cheatsheet For Bitwise Operations
=================================
## Basics

#### C language supports the following bitwise operators.

1. | – Bitwise OR
2. & – Bitwise AND
3. ~ – One’s complement
4. ^ – Bitwise XOR
5. << – left shift
6. \>> – right shift


### Bitwise OR - |
    
          1100
       or 1010 
          ----
          1110

Bitwise OR is used to Turn-On bits


### Bitwise AND - & 
    
          1100
      and 1010 
          ----
          1000

Bitwise OR is used to Turn-off bits

### Bitwise NOT - ~
      and 1010 
          ----
          0101
          
### Bitwise XOR - ^
    
          1100
      xor 1010 
          ----
          0110

Bitwise OR is used to Turn-On bits



### Left Shift Operator – <<
The left shift operator will shift the bits towards left for the given number of times.
        
        int a = 8<<1;
        
        Pos   7 6 5 4 3 2 1 0
        Bits  0 0 0 0 1 0 0 0
        
        After one leftshift 
       
        Pos   7 6 5 4 3 2 1 0
        Bits  0 0 0 1 0 0 0 0
   
Left shifting 1 time, is equal to multiplying the value by 2.    


### Right Shift Operator – >>
The right shift operator will shift the bits towards right for the given number of times.
        
        int a = 8>>1;
        
        Pos   7 6 5 4 3 2 1 0
        Bits  0 0 0 0 1 0 0 0
        
        After one leftshift 
       
        Pos   7 6 5 4 3 2 1 0
        Bits  0 0 0 0 0 1 0 0
   
Right shifting 1 time, is equal to dividing the value by 2.    


#### While performing shifting, if the operand is a signed value, then arithmetic shift will be used. If the type is unsigned, then logical shift will be used.

In case of arithmetic shift, the sign-bit ( MSB ) is preserved. Logical shift will not preserve the signed bit. Let’s see this via an example.


        #include<stdio.h>
        
        int main() {
            signed char a=-8; 
            signed char b= a >> 1;
            printf("%d\n",b);
        }

In the above code, we are right shifting -8 by 1. The result will be “-4″. Here arithmetic shift is applied since the operand is a signed value.

        #include<stdio.h>

        int main() {
           unsigned char a=-8;
           unsigned char b= a >> 1;
           printf("%d\n",b);
       }
       
Note: Negative number are represented using 2′s complement of its positive equivalent.

        2's compliment of +8 is
        
        1111 1000

        Right shifting by 1 yields,

        0111 1100 ( 124 in decimal )
        
        
The above code will result in 124 ( Positive value ). Here logical shift is applied since the operand is unsigned, and it won’t preserve the MSB of the operand.

## Hints

- Use the following formula to turn off the rightmost 1-bit in a word, producing 0 if none (e.g., 01011000 &rarr; 01010000):

            x & (x - 1)

- Use the following formula to turn on the rightmost 0-bit in a word, producing all 1’s if none (e.g., 10100111 &rarr; 10101111):

            x | (x + 1)
  
- Use the following formula to turn off the trailing 1’s in a word, producing x if none (e.g., 10100111 &rarr; 10100000):

            x & (x + 1)
            
- Use the following formula to turn on the trailing 0’s in a word, producing x if none (e.g., 10101000 &rarr; 10101111):
    
            x | (x - 1)
            
- Use the following formula to create a word with a single 1-bit at the position of the rightmost 0-bit in x, producing 0 if none (e.g., 10100111 &rarr; 00001000):

            ~x & (x + 1)

- Use the following formula to create a word with a single 0-bit at the position of the rightmost 1-bit in x, producing all 1’s if none (e.g., 1010 1000 &rarr; 11110111):

            ~x & (x - 1)

- Use one of the following formulas to create a word with 1’s at the positions of the trailing 0’s in x, and 0’s elsewhere, producing 0 if none (e.g., 01011000 &rarr; 00000111):
        
            ~ x & ( x - 1 ), or
            ~ (x | - x ), or
            ( x & - x ) - 1
            
- Use the following formula to create a word with 0’s at the positions of the trailing 1’s in x, and 0’s elsewhere, producing all 1’s if none (e.g., 10100111 &rarr; 11111000):

            ~ x | (x + 1)
            
- Use the following formula to isolate the rightmost 1-bit, producing 0 if none (e.g., 01011000 &rarr; 00001000):
        
            x & (-x)
           
- Use the following formula to create a word with 1’s at the positions of the rightmost 1-bit and the trailing 0’s in x, producing all 1’s if no 1-bit, and the inte- ger 1 if no trailing 0’s (e.g., 01011000 &rarr; 00001111):

           x ^ (x - 1)
        
- Use the following formula to create a word with 1’s at the positions of the rightmost 0-bit and the trailing 1’s in x, producing all 1’s if no 0-bit, and the integer 1 if no trailing 1’s (e.g., 01010111 &rarr; 00001111):

            x ^ (x + 1)
            
- Use either of the following formulas to turn off the rightmost contiguous string of 1’s (e.g., 01011100 &rarr; 01000000):

            (((x | (x - 1)) + 1) & x), or
            ((x & -x) + x) & x
            
                       
Some Quick Example 
==================

### Find out whether an integer is a power of two.

A power of two will look like this in memory:

        00000001 or
        00000010 or 
        00000100 or
        ...
        
Subtract 1 from this will get odd number
        
          00100000  ---- A
        - 00000001
          --------
          00011111  ---- B
          
Not bitwise and of A and B will give you 0
        
        int is_power(int x)
        {
            return !((x-1) & x);
        } 



### You're given anunsorted array of integers where every integer appears exactly twice, except for one integer which appears only once.Write an algorithm that finds the integer that appears only once.

XOR all the values of the array together! Since XOR iscommutative and is its own inverse, each integer in the array that appears twice will cancel itself out, and we'll be left with the integer we're looking for. This takes O(n) time and O(1) space. We told you bitwise stuff was handy!

        int oddManOut(int[] array) {
            int val = 0;
            for (int i = 0; i < array.length; i++) {
                val ^= array[i];
            }
            return val;
        }
        
### How to count the number of set bits in an integer variable?

The "bitwise and" of x with x - 1 differs from x only in zeroing out the least significant nonzero bit: subtracting 1 changes the rightmost string of 0s to 1s, and changes the rightmost 1 to a 0. If x originally had n bits that were 1, then after only n iterations of this operation, x will be reduced to zero. The following implementation is based on this principle.

        //This is better when most bits in x are 0
        //It uses 3 arithmetic operations and one comparison/branch per "1" bit in x.
        int popcount_4(uint64 x) {
            int count;
            for (count=0; x; count++)
                x &= x-1;
            return count;
        }
        
### Write a function to find nonzero numbers. This function should return 1 for nonzero numbers, 0 otherwise. `if`, `else` cannot be used.

        int isnonzero(unsigned int n) {
                // Set LSB if any of the bit is set
                for(i = sizeof(n) - 1; i > 0; i--) {
                    n |= n >> i;
                }
                return n & 1;
        }
        
Much simpler version:

        int isnonzero(unsigned int n) {
            return ((n | (~n + 1)) >> 31) & 1;
        }
        
#### TODO: Complete this
[Source] 

1. Hacker's delight book