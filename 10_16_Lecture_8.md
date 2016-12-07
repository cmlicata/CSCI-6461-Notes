Lecture 8 - Instruction level Parallelism (continued)
------------------------------------------------------

Superscalar Terminology
-----------------------
    - Superscalar - Able to issue > 1 instruction / cycle
    - Superpipelined - Deep, but not superscalar pipeline, e.g., MIPS R500 has 8 stages
    - Out-of-order - Able to issue instructions out of program order
    - Speculation - Execute instructions beyond branch points, possibly nullifying later
    - Register renaming - Able to dynamically assign physical registers to instructions
    - Retire unit - Logic to keep track of instructions as the complete

#### Control-Dependency

 - Every instruction is *control dependent* on some set of branches
     ```
  if p1
       S1;
  if p2
       S2;
    ```
  - ```S1``` is control dependent on ```p1```, and ```S2``` is control dependent on ```p2``` but not on ```p1```
  - *Control dependencies must be preserved to preserve program order. *

     ```
      DADDU	 R2,R3,R4
      BEQZ	  R2,L1
      LW		R1,0(R2)

      L1:
     ```      
    - Can't move ```LW``` before ```BEQZ```
    - Because  ```LW``` is essentially the ```else``` statement in all of this.
 - ** SUMMARY **
    - *A dynamic execution scheme must produce the **same** register/memory contents as a sequential execution, any time it is stopped*

Waiting for the outcome of branches significantly affects parallelism
**Speculation:** fetch, issue, and execute instructions as if branch predictions were always correct

 #### Program Statement Types
  - Generally, statements and definitions in a program can be divided into three types:
       1. Things which must be run and are mandatory
       2. Things which do not need to be run because they are irrelevant.
       3. Those statements which cannot be proven to be in either of the first two groups.
  - The first group **does not** benefit from speculative execution because they need to run anyway.
  - The second group can be quietly discarded because they are out of the main stream of execution (i.e. branch not taken).
  - The third group (i.e. those that cannot be proven to be in either of the first two groups) is the target of speculative evaluation, as they can be run concurrently with the mandatory computations until they are needed or shown to be of the second group.
      - This concurrency means that speculative execution can be parallelized...
Speculative Execution
---------------------
fd
