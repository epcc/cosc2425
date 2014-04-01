
Control Flow
-----------------

    A brief description about what the topics are and the usage in programming.

   Control flow is the order in which individual instructions are
   executed. In the MSP430 microcontroller and most architectures,
   instructions are executed in the order they appear in the program.
   The Program Counter (PC) register keeps track of the address of the
   next instruction to be executed. Before a program can be executed,
   the PC needs to be set to the address of the first instruction of
   the program. For example, take the following program:

   ```assembler
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

   **Exercise:** Given a small sequence of instructions and a starting
        address, figure out the address of every instruction.

Branching
---------------------