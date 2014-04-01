
Control Flow
============

Control flow is the order in which individual instructions are
executed. In the MSP430 microcontroller and most architectures,
instructions are executed in the order they appear in the program.
The Program Counter (PC) register keeps track of the address of the
next instruction to be executed. Before a program can be executed,
the PC needs to be set to the address of the first instruction of
the program. For example, take the following program:

```
A000: MOV r4, r5
A002: ADD r10, r5
```

The first instruction lives in address **A000**. The instruction
occupies 1 word (2 bytes), so the next instruction lives in address
A002. 

Before the program can be executed, the Program Counter (PC)
register will be set to **A000**. The processor will execute the
instruction at addres **A000**, then it will increment the PC so
that it points to the next instruction. In this example, the PC
will be now set to **A002**. *Branching* is changing the flow when
certain conditions are true. 

Remember that on the MSP430 the size of the instruction depends on
the addressing mode of the parameters.

Exercise
--------

Given a small sequence of instructions and a starting address, figure
out the address of every instruction.

Branching
=========

Conditions and Loops
--------------------

All computer programs need to make decisions. The familiar \texttt{if-then-else} construct found in Java-like programming languages tests a condition and then executes a block of code if the condition is true or a different block if the condition is false. Such constructs can test for one or more conditions. For example, to check whether the variable \texttt{year} is a leap year, one might write:

```java
if(year % 4 == 0 && year % 100 != 0) || (year % 400 == 0))
{
     isLeapYear = true;
}
else
{
     isLeapYear = false;
}
```

Such comparisons are ultimately carried out by the processor in a simple way: two values are compared by performing a subtraction. A subtraction ---just like most arithmetic operations--- sets *arithmetic flags* to record whether the result is correct, has the wrong sign, or is incomplete. Programmers, in turn, can use these flags to determine the relationship between two values. Special instructions called *jump instructions* are then used to alter the flow of the program if certain flags are set.

When several conditions are to be tested, each one has to be checked by an individual subtraction operation as there is no way of checking multiple conditions at once.

Comparisons in a Computer
-------------------------

Comparing $A$ and $B$ is simple: we know that if $A=B$, then $A-B=0$. If $A>B$, then $A-B>0$; and if $A < B$, then $A-B<0$; however, we cannot blindly trust the result of $A-B$ in a computer: because numbers are stored in a finite amount of bits, arithmetic operations may produce results too big (or too small) to be represented. We need to handle such situations adequately.

The Negative (N) and Zero (Z) Flags
-----------------------------------

The negative and zero flags are set when the result is negative (when the most significant bit is $1$) and when the result is zero. The carry and overflow flags have subtle implications that merit more detail.

The Carry (C) Flag
------------------

The carry flag is set when a carry happens in the most significant  bit of an arithmetic operation. For example, adding the 16-bit numbers `0x0FFF` and `0xFF00` results in a carry in the most significant bit:

```
 11111111        
  0000111111111111
+ 1111111100000000
------------------
  0000111011111111
```


The carry flag signals that the result does not fit in the number of
bits we are working with. In the previous example, the carry is set
because the actual result does not fit in 16 bits. However, if we had
17 bits to store the result instead of 16, the carry of the $16^{th}$
bit would fall naturally into the $17^{th}$ bit and there would be no
actual carry. Obviously, the number of bits in a word is always
limited and a carry is always possible.

As mentioned before, we perform subtractions to compare two values.
However, subtractions in most processors are performed not by
subtracting bit-by-bit, but by adding one operand to the **2's
complement** of the other operand. So, to compute $5-3$ the MSP430 adds
$5$ to the **2's complement** of 3 (which is $-3$) as shown below:

```
 11111111111111 1 
  0000000000000101 (the number 5)
+ 1111111111111101 (the 2's complement of 3, -3)
------------------
  0000000000000010 (2, the correct result of 5-3)
```

However, notice how the carry flag is not set when the second operand
is greater than the first, like when subtracting $5-6$:

```
  0000000000000101 (the number 5)
+ 1111111111111010 (the 2's complement of 6, -6)
------------------
  1111111111111111 (-1, the correct result) 
```

Knowing this, we can compare two unsigned numbers $A$ and $B$ by
subtracting $A-B$ and checking the carry flag. From the above
examples, we can deduce that $A \geq B$ if the carry flag is set, and
that $A < B$ if it is not set. The opposite conditions, namely $A \leq
B$ and $A > B$, can be checked by reversing the subtraction order.

Signed Numbers
--------------

This approach, however, does not always work with signed numbers. For
small negative values it works with no problems; for example, if
$A=-5$ and $B=-3$, everything goes as expected when performing
$(-)5 - (-)3$:

```
                    11  (no carry)
       1111111111111011 (-5)
     + 0000000000000011 (the 2's complement of -3, that is, 3)
     ------------------
       1111111111111110 (-2) 
```

Performing $A-B$ does not produce a carry and we can deduce that $A <
B$, which is true because $-5 < -3$. If we reverse the subtraction
order, everything still works as expected: the carry flag is set and
we can assume that $-3 \geq -5$, which is true.

This does not work when the signed numbers get close to their ranges.
For example, if we compare the smallest possible 16-bit negative
value, $-32,768$ to $2$ by performing a subtraction, the carry flag
does not tell us the whole story:

```
      1
       1000000000000000 (-32,768, the smallest negative value)
     + 1111111111111110 (the 2's complement of 2, that is, -2)
     ------------------
       0111111111111110 (32,766) 
```

Here, the carry flag was set, telling us incorrectly that $-32,768 \geq 2$. However, the exact same operation makes complete sense if we interpret the operands as unsigned numbers:

```
      1
       1000000000000000 (32,768)
     + 1111111111111110 (the 2's complement of 2, that is, -2)
     ------------------
       0111111111111110 (32,766) 
```

The result is the same, but now looking at the operands as positive values, the carry bit is telling us that $32,768 \geq 2$, which is correct. It is clear that we will need to use another approach when dealing with signed numbers.
