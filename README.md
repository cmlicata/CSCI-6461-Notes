Test Questions
--------------

1. *Explain, using a diagram to illustrate your explanation, how a ```page table register```, ```a page table```, and a ```virtual address``` are used in a Virtual Memory system to produce a ```physical address```.*  [ 6 marks ]
</br>

2. *You're responsible for the design of cache for a new processor. **Specify, giving reasons**, which combination of the following caching techniques would be most suitable for level 1 cache **(In all CPUs)** and for level 3 cache **(A Shared Cache Amongst all the CPUs)**.* [ 6 marks ] </br>
**Placement policy: Direct Mapping and Set-Associative Mapping.
    Replacement update policy: FIFO and LRU**
    </br>
    - **Answer:** As L1 caches are in the critical path of instruction execution, **hit time** ( ```Time to Access RAM + Time to Determine Hit/Miss``` ) is the most important parameter. For an L1 cache I would use a set-associative mapping placement policy and use the LRU algorithm for a replacement update policy.  This is because the LRU algorithm is extremely efficient and with a 2-way associative policy, we avoid a lot of the unnecessary conflict misses that you normally get with a direct mapping policy.  For an L3 cache I would use Direct mapping placement policy because it is physically indexed, therefore the direct mapping policy with a FIFO replacement policy would be best for the L3 cache.
    </br>
3. *Write down two implications of the fact that page faults causes delays of millions of clock cycles.*
    - **Answer:**
        - Page faults, by their very nature, degrade the performance of a program or operating system and in the degenerate case can cause thrashing. Invalid page faults can even result in process termination by the CPU.
</br>
4. *Describe the type of data dependencies and an example of each.*
    - **Answer:**
        1. **Flow dependency: (i.e. read-after-write (RAW) )**
            - occurs when an instruction depends on the result of a previous instruction:
            ```
              1. A = 3
              2. B = A
              3. C = B
            ```

          - Instruction ```3``` is truly dependent on instruction ```2```, as the final value of ```C``` depends on the instruction updating B.
          - Additionally, instruction ```2``` is truly dependent on instruction ```1```, as the final value of ```B``` depends on the instruction updating ```A```.
          - Since instruction ```3``` is truly dependent upon instruction ```2``` and instruction ```2``` is truly dependent on instruction ```1```, instruction ```3``` is also truly dependent on instruction ```1```.
         - **Instruction level parallelism** is therefore not an option in this example.
         </br>
        2. **Anti-Dependency (i.e. write-after-read (WAR) ):**
            - An anti-dependency, also known as write-after-read (WAR), occurs when an instruction requires a value that is later updated. In the following example, instruction ```2``` anti-depends on instruction ```3```.
            ```
               1. B = 3
              2. A = B + 1
              3. B = 7
            ```

            - The ordering of these instructions cannot be changed, nor can they be executed in parallel (possibly changing the instruction ordering), as this would affect the final value of ```A```.

            - An anti-dependency is an example of a name dependency. That is, renaming of variables could remove the dependency, as in the next example:
            ```
            1) B = 3
            N) B2 = B
            2) A = B2 + 1
            3) B = 7

            ```
            - A new variable, ```B2```, has been declared as a copy of ```B``` in a new instruction, instruction ```N```. The anti-dependency between ```2``` and ```3``` has been removed, meaning that these instructions may now be executed in parallel.
            - However, the modification has introduced a new dependency: instruction ```2``` is now truly dependent on instruction ```N```, which is truly dependent upon instruction ```1```. As flow dependencies, these new dependencies are impossible to safely remove.

        3.  **Control-Dependency:**
            - An instruction ```B``` has a control dependency on a preceding instruction ```A``` if the outcome of ```A``` determines whether ```B``` should be executed or not. In the following example, the instruction S<sub>2</sub> has a control dependency on instruction  S<sub>1</sub>. However, S<sub>3</sub> does not depend on  S<sub>1</sub> because
            S<sub>3</sub> is always executed irrespective of the outcome of S<sub>1</sub>.

                ```
                S1.  if (a == b)
              S2.     a = a + b
              S3.  b = a + b
                ```
            - Intuitively, there is control dependence between two statements ```A``` and ```B```. If ```B``` could be possibly executed after ```A```. The outcome of the execution of ```A``` will determine whether ```B``` will be executed or not.
5. *What is the sequence of action taken by the processor, the memory controller, and the disk drive to make this work?* [ 2 marks ]
    1.	Device driver is told to transfer disk data to buffer at address ```X```
    2.	Device Driver tells disk controller to transfer ```C``` bytes from disk to buffer at address ```X```.
    3.	Disk controller initiates DMA transfer.
    4.	Disk Controller sends each byte to DMA controller.
    5.	DMA controller transfers bytes to buffer ```X```, increasing memory address and decreasing ```C``` until ```C=0```.
    6.	when ```C=0```, DMA interrupts CPU to signal transfer completion

6. a) *What is the data structure and algorithm for branch prediction?*
    - The data structure is a Branch Target Buffer(BTB) (aka Branch History Table (BHT)) with a 1-bit scheme.  
    - The algorithm used can be described as the following:
        ```
          # Send PC to memory and BHT.  
          if ( entry.isFound() ); then
            # Send out the Predicted PC
            if ( branch.isTaken() ); then
                # Branch correctly predicted; continue execution with no stalls
            else
               # Mispredicted Branch;
               # kill fetched instruction;
               # restart fetch at other target;
               # delete entry from BTB
           fi
          else
           if (instruction.isTakenBranch() ); then
              # Enter branch instruction address and next PC into BTB
           else
              # Normal instruction execution occurs
          fi
      ```

   b) *When and why does a 1-bit Branch history table mispredict?*
      - A 1-bit Branch history mispredicts *twice*, on the first and last iterations of every loop.
          - This is due to the end of loop case when it exits instead of looping.
              - Mispredict of the first one due to the fact that the loop branch was not taken as ```counter = 0```.
              - Mispredict of the last loop because it is not taken due to the fact that the boundary condition of the loop  is true.  (e.g. loop branch that is taken nine times in a row and then moves on ( ```for i = 0; i < 10 ; i++ ```))
              - Therefore, it is only 80% accurate.
7.  *List the advantages and disadvantages of both I/O mapped (port mapped) I/O and Memory-Mapped I/O.*

  - Memory-Mapped I/O -
      Peripheral connected to CPU address and data lines (like memory)
         - writing/reading to the associated addresses performs data transfer
     - Advantages of Memory-Mapped I/O
         1. The CPU can use any instruction that accesses memory to transfer data to/from a Memory-Mapped I/O device (i.e. you don't need special instructions reserved for I/O ).
         2. Can write large amounts of data really quickly (suitable for devices like GPUs)
     - Disadvantages
         1. I/O consumes addresses in the memory map and wastes a lot of space.
  - I/O mapped (i.e. port-mapped) I/O
      - Uses special instructions to access I/O specific ports
         - e.g. ```IN``` and ```OUT``` instructions in Intel 80x86
      - Devices/buses connected to ports

           - Advantages:
                - Address space not used (ports used)
           - Disadvantages
                - You have to use special instructions to access memory
                - It is quite small
                    - While some peripherals use fewer than 16-bit I/O addresses, a few devices like video display cards can occupy millions of different I/O locations.
                - Have to poll the ports until data is available, which wastes CPU cycles.
8. *What is vector chaining?*
 - Suppose:
       ```
      	MULV	V1,V2,V3
    ADDV   V4,V1,V5
      ```
      - **vector chaining**: vector register (V1) is not as a single entity but as a group of individual registers, then pipeline forwarding can work on individual elements of a vector
      - **Flexible chaining**: allow vector to chain to any other active vector operation => more read/write ports, e.g. pass the result from one vector operation to another vector operation
9. Superscalar Architectures
    a) *How does a microprocessor achieve **superscalar capability**?
          - Superscalar describes a microprocessor design that makes it possible for more than one instruction at a time to be executed during a single clock cycle.
          - The processor or compiler determines whether an instruction can be executed independently of other sequential instructions or whether or not it has a dependency on another instruction and must be executed in sequence with it.  
          - The processor uses multiple execution units to simultaneously carry out two or more independent instructions at a time.  
     b) *Describe how renaming of registers contributes to a superscalar capabiliity?*
          - Different instructions take different amount of time, period.  In practice, it is highly possible for microprocessor to execute shorter instructions faster than other instructions.
          - In such cases, if a series of independent instructions can be executed much efficiently, but must wait to execute in order which results in stalls.  So by using register renaming, the microprocessor is able to better handle data dependencies and perform out of order execution.  
     c) *What is role of the reservation station in a superscalar architecture?*
          - Reservation stations permit the CPU to fetch and re-use a data value as soon as it has been computed, rather than waiting for it to be stored in a register and re-read. When instructions are issued, they can designate the reservation station from which they want their input to read. When multiple instructions need to write to the same register, all can proceed and only the (logically) last one need actually be written. It checks if the operands are available (RAW) and if execution unit is free (Structural hazard) before starting execution.


Lecture 3 - Instruction Set Architecture
----------------------------------------

- ISA - visible to assembly level programmers/compilers
    - Not microinstructions
    - Important design issue for CPU designers
        - Features can be added to the CPU after original design is obsolete
        - Difficult to change ISA after CPU is in production

- Why not put every instruction on a chip?
    - Real-estate on a chip - silicon budget/transistors
        - More transistors => greater cost
    - Can't anticipate everything people will won't
    - Legacy support
        - Need to support all instructions you put on a chip
    - Complexity - hard to learn

- Interpreting words
    - Little endian -  least significant byte in smallest position
    - Big endian - most significant byte in smallest position

- Types of architectures (pros/cons in slides):
    - Stack
    - Accumulator
    - Memory-Memory
    - Register-Memory
    - Register-Register

Lecture 4 - Memory and Cache
----------------------------
- Cost, locality, and speed

- Memory Hierarchy
  - Processor -> Cache Levels (L1, L2, etc.) -> Main Memory -> Secondary Memory (disks)
  - Main memory slower than processor
      - Faster memory => cost
      - High decoding times, etc. => larger memory is slower

      - Want CPU to get data as fast as possible:
          - Solution: Implement cache

- Memory History
  - Mercury/acoustic delay lines - memory in the form of electrical pulses transduced into mechanical waves
  - Magnetic drum, vacuum tubes
  - CRT
  - Magnetic cores
  - Semiconductor
      - (D)ynamic RAM
          - Needs cells recharged every few microseconds
      - (S)tatic RAM
          - Doesn't need recharge
- Parity vs. Non-Parity
  - Even vs. odd parity
  - Parity not done much anymore - memory has become relatively reliable.
      - Used in some mission-critical systems

- Modern RAM - typically 8 memory chips - one for each bit in a byte. Ninth chip for the memory controller.

- Memory Interleaving/Banking
  - Can only access one word of memory at a time
      - Since processor is much faster than RAM (terms of clock speed), processor wastes cycles while waiting for words to be fetched from memory.

  - Interleaving - distributing addresses across multiple banks
      - Allows simultaneous (parallel) accessing of words in memory if the words are in separate banks
      - May conflict with caching

  - DIMM (Dual Inline Memory Module) - contains multiple chips

- Memory System Design
  - Locality - programs access a relatively small portion of address space at any instant of time
  - Temporal locality - if and instruction/data is used now, there is a good chance that it will be used again in the future
  - Spatial locality - "" then there is a good chance that the instructions or data items that are in memory following/preceding will soon be used

  - Good idea to move these instructions/data from slow memory to fast memory (cache)
      - This prediction will not always be correct
      - Today, caches are > 75% successful

- Cache
  - Small, fast memory
  - Arranged in blocks
      - Block from memory loaded into a cache line
      - When processor emits an EA, the address goes to the cache - cache controller checks cache lines = HIT
          - Else MISS - need to go to main memory, add the block to cache and reinitiate the cache check
          - Processor <--> Cache <--> Memory
  - Cache comprised of cache lines - each with an address tag and data
      - Address tag - like an array base index based on word boundary
          - i.e. 00001XXX, 00002XXX, 00003XXX, where XXX would be the index into the data blocks at a specific address tag

- Cache Performance
  - Memory access time = cache hit time OR cache miss rate * miss penalty
  - Types of misses:
      - Compulsory - always occurs the first time.
      - Capacity - reduce with increase in capacity size.
          - Need to push something out of the cache.
      - Conflict - reduce with level of associativity.

- Cache Topology
  - Initially, both instructions and data were stored in the same cache (unified)
      - Since then, compilers have split up address and data space
      - Created split caches
      - Need to keep track of base address and base data

- Cache Writing
  - Write Buffer needed between cache and memory
      - Used to update memory/cache
          - Write-through - when you update the cache, update the memory
              - Memory is slower than the cache - memory is out of sync for small amount of time
              - Use a writing buffer to write to memory when the memory controller determines memory is idle.
                  - Requires extra electronics for the buffer
              - Have enough data/instructions in cache so memory can go idle for a time
          - Write-back - Don't update memory until the cache line needs to be replaced (using dirty bit)

- Caching Algorithms
  - Fully associative - a block in memory can be placed on any cache line
  - 2-way Set Associative - a block in memory is mapped to a specific set of cache lines  (memory % num sets)
  - Direct map - a block is memory is mapped to a single cache line (memory % num cache lines)

- Cache Replacement on Cache Miss and Full Cache
  - Easy for direct map - just replace the cache line that the new memory block should go to
  - Algorithms:
      - Least Recently Used - look for oldest time stamp
      - FIFO
      - Least Frequently Used - look at counter
      - Random

      - LRU most efficient, simple to implement with good results
      - FIFO is simple to implement
      - Random is simplest to implement and has surprisingly good results

  - Special bits
      - Dirty bit - designates modified cache line (has to be written to memory)
      - Valid bit - specifies whether a given cache line is related to a currently executing program

- Cache Optimizations
  - Reducing hit time
  - Increasing cache bandwidth
  - Reducing miss penalty
  - Reducing miss rate
  - Reducing miss penalty or miss rate via parallelism
Virtual Memory
--------------
- Illusion that there is more memory than the physical memory size
  - Pages in memory are swapped out to disk when they're not being used

- Why?
  - Program sizes have grown larger than available physical memory
      - Address space needed and seen by programs is usually larger than available memory
  - Swapping of whole programs is cost prohibitive given relative speeds of CPU, memory, and disk

- Virtual memory
  - First implemented in 1954 at Univ of Manchester, UK
  - Processor emits "virtual address" from program - needs to translated to a physical memory address
      - Memory Management Unit (MMU) does the translation
      - Pages may need to get fetched off disk containing the requested data

- Checking Memory Bounds
  - Generate address larger than physical memory
  or
  - Generating an address outside address space of program (segmentation fault)
  - Use special registers:
      - Base register - first address of program
      - Bounds register - base + bounds = last address of program

- Memory Fragmentation
  - As users come and go, storage become "fragmented" (little bits of free space scattered around)

- Paging
  - Programs broken into fixed page sizes
  - OS manages loading/storing pages into memory/from disk
  - Page fault - when CPU refers to a location in a page which in not in memory
      - Have to replace a page in memory
      - May have to write "dirty" pages back to disk
  - Each process has its own page table

- Process Execution
  - OS brings in only a few pages of the program
  - Each page/segment table entry has a "presence bit" set if the corresponding page is in memory
  - Resident set - the portion of the process that is in memory
  - Interrupt generated on a page fault
      - Instruction is retried
  - Effectively, the OS uses main memory as a page cache.

- Thrashing
  - OS spends more time moving pages in and out of memory than it does doing computation

- Page Table
  - Contains the physical address of each page in memory
  - Allows a program's pages to be in memory non-contiguously
  - Each entry contains a present bit to indicate whether the page is in main memory or not
      - If not in memory, the entry may contain the address of that page on disk or an index into the disk page table
  - Single register used to hold the physical address of the page table
  - Sometimes, the page table is stored in virtual memory
      - But then you have to translate address to page table from a virtual address to a physical one

- Sharing Pages
  - If the same code is shared among different processes (non self-modifying), you can keep just one page in main memory

- Translation Look-aside Buffer
  - Like a cache for the page table

- Segmentation
  - A program is not organized in pages, but in terms of functions, subroutines, etc.
  - Each program has its own segment table
      - Each function can become a segment, but typically smaller functions are clumped together into segments
  - Rather than dividing a program into fixed size pages, they're divided in variable length segments
  - Entries in the segment table associated with a length and base field
  - Protection bits can be set by the programmer (read only/read write/supervisor/user), whereas in paging, these bits are set by the OS.

- Fetch policy
  - Determines when a page should be brought into main memory
  - Demand paging - only brings pages into main memory when a reference is made to a location on the page
  - Prepaging - brings in more pages than needed
      - Based on locality of references - may be more efficient to bring in pages that reside contiguously on the disk

- Placement Policy
  - Determines where in real memory a process piece resides
  - Segmentation systems:
      - First-fit, next fit, ...
  - Paging
      - Hardware decides

- Replacement Policy
  - LRU, random, FIFO, clock (circular queue)

- Resident Set Size
  - How many frames to allocate to the process
  - Increasing resident set decreases page fault rate
  - Decrease if page fault rate is very low
      - Too low -> thrashing

- Working Set
  - The working set for a process at time t, W(D,t), is the set of pages that have been referenced in the last D virtual time units.
      - Virtual time - time elapsed while the process was in executed
  - Monitored for a specific process to determine the resident set size
  - Used to define an upper bound and lower bound for page fault rates in order to dynamically change resident set size
  Chapter 6 - I/O
  ---------------

  - Forms of I/O:
      - I/O mapped I/O
          - Uses special instructions to transfer data between computer and "outside world" (ports)
      - Memory mapped I/O
          - Uses special memory locations in the address space of the CPU to communicate with real-world devices
      - Direct Memory Access (DMA)
          - DMA controller received instructions from CPU
          - Sets up and performs transfer of data

  - Memory-Mapped I/O
      - Peripheral connected to CPU address and data lines (like memory)
          - Writing/reading to the associated addresses performs data transfer
      - Suitable for devices that need to move large amounts of data quickly (GPUs)
      - Issue: process writes/reads directly to the address space of the device
      - Advantages:
          - The CPU can use any instruction that access memory to transfer data to/from a memory-mapped I/O device
              - i.e. don't need special instructions reserved for I/O
      - Disadvantages:
          - I/O consumes addresses in the memory map (lots of wasted space)

  - I/O mapped I/O
      - Uses special instruction to access I/O ports
          - Ex. IN and OUT instructions in Intel 80x86
      - Devices/buses connected to ports
      - Advantages:
          - Address space not used (ports used)

      - Disadvantages
          - You have to use special instructions to access memory
          - Small
          - Have to poll the ports until data in available (wastes CPU cycles)

  - Direct Memory Access
      - "Old" I/O approaches needed to use multiple instructions to move large pieces of data off disk into memory (i.e. moving 1024 words requires 1024 instructions (loop))
          - Inefficient to handle one byte at a time
      - DMA controller introduced (extra circuitry in front of CPU memory bus)
          - CPU sends out an address or number of words to DMA controller
          - DMA controller handles data transfer
              - Allows to CPU to do other work
              - DMA controller generates an interrupt when data transfer is complete
          - The CPU and DMA controller cannot bot use the address/data buses at the same time
          - Allows parallelism if:
              - CPU has a cache
              - CPU is executing code
              - Accesses data in the cache (leaves bus free for DMA controller)

  - Interrupt-Drive System
      - CPU has an interrupt-request line that are sensed after every instruction
      - If an interrupt is raised, current state is saved, and control is transfered to the appropriate interrupt handler
          - After executing, the handler performs a return from interrupt instruction

  - Traps
      - A software-initiated interrupt
      - Used to request some service from the OS
      - Hardware-initiated traps are used for hardware faults (memory faults)

  - I/O Processors
      - Separate microprocessor-based systems that handles I/O operations
          - Talks to device controllers, receives I/O requests to I/O processor




  - Buses
      - Types
          - Processor-memory
          - I/O
          - Backplane
      - Set of control lines and data lines
      - Creates a bottleneck:
          - Physical factors: length of bus, number of devices
          - Lots of devices increases propagation delays
      - Synchronous buses
          - Clock in control lines
      - Asynchronous buses
          - Not clocked
          - Need a handshaking protocol - read request, data ready, ACK

Performance
-----------

- Performance = Time(CPU) + Time(IO) - Time(Overlap)
- Computation Time factors:
    - Number of instructions executed = Instruction Count (not always = program size (not all instructions are executed))
    - Avg. number of cycles per instruction (CPI)

- Instruction Level Parallelism (ILP)
    - Principle that there are many instructions in code that don't depend on each other
    - Possible to execute those instructions in parallel
    - Assumes multiple functional units
    - Issues:
            - Build a compiler to analyze code and generate alternative sequences of instructions
            - Smart hardware that dynamically schedules instruction execution at run-time

- Terminology
    - Basic block - a set of instructions between entry points and between branches (has only one entry and exit)
    - Loop level parallelism - parallelism that exists within a loop
    - Loop unrolling - either the compiler or the hardware is able to exploit the parallelism inherent in a loop

- Ways to improve performance:
    - Reduce clock cycle time
        - Technology, implementation
    - Reduce number of instructions
        - Improve instruction set
        - Improve compiler
    - Reduce cycle, instruction
        - Improve implementation

- Pipelining
    - Mechanism for breaking a task into multiple subtask - each separate from each other - and performing the subtasks concurrently
    - Clock divider divides clock cycles into subcycles for the pipelines to use
    - Pipeline tries to issue a result every clock cycle

- Limits to Pipelining
    - Structural hazards: hardware cannot support this combination of instructions
        - Instruction in pipeline may need a resource be in use by another instruction in the pipeline
        - Can be avoided by adding more hardware (ex. more ALUs so two adds can be done concurrently)
    - Data hazards
        - Instruction depends on result of another instruction still in the pipeline
        - Data hazards due to register operands can be determined at the decode stage
            - But those due to memory addresses can only be determined after computing the effective address
    - Control hazards
        - Caused by delay between fetching instructions and decisions about changes in control flow

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

Lecture 9 - Vector Operations
-----------------------------
- Final Exam - Dec. 10, MPA B07 - 5:20 - 7:20 PM
- Part 4 - due Dec. 14 (midnight) Sunday
___
- Seymour Cray - Cray (vector) computers, improved end-end speed of computers

- Improving Performance
    - Vectors - collections of like-numbers
        - Performance can be improved if we structure hardware to deal with vectors
        - Vectors have consistent patterns:
            - Vector instructions access memory with a known pattern
            - No data cache required - want to do block operations
- Vector Computer
    - Vector register - fixed-length bank (commonly 64 elements) holding a single vector
        - Usually comprised of normal GP registers and floating point registers
        - Provide input data to vector functional units
    - Vector functional unit - fully pipelined, can start a new operation on every cycle
    - Vector load/store unit - loads or stores a vector to or from vector
        - Does a block move to fetch the vector from memory in one instruction
    - Vector length control - Natural length of the vector (how many elements need to be operated on)

    - Types of vector processors
        - Vector-Registers
            - Most common
            - All vector operations occur in the vector registers
            - Vector counterpart of a load-store architecture
        - Memory-Memory processors
            - All vectors instructions operate on memory
            - Comparatively slower (memory slower than CPU)
    - Most common floating/vector operation -> S = ax + b

    - Vector processor internals
        - A scalar processor
            - Scalar registers/functional units
        - Vector register file
        - Pipelined vector functional units
        - Types of addressing
            - Unit stride
                - Increment index by 1 for every loop (will process every element in the vector)
                - Fastest, easiest to optimize
            - Non-unit (constant) stride
                - Incrementing index by x for every loop (will process every xth element)
                    - ex: stride 2 = every other element
                - Harder to optimize for all possible strides
            - Indexed (gather-scatter)
                - Essentially register indirect with vectors
                - Uses two vectors
                    - Pulling a value from the first vector is used as the index into the second vector
                    - First vector processed according to the stride value
                - Good for sparse arrays of data
                - Gather = load, scatter = store

- Vector Terminology
    - Vector Start-up Time - How long it takes to start up the vector pipeline
        - i.e. number of cycles required prior to the generation of the first result
            - Afterwards, a result is generated for every cycle
        - Lots of overhead for vectors with a low number of elements (ex. 10 cycle startup to process a 4 element vector)
            - Negligible overhead for vectors with a high number of elements

- Issues
    - If the length of the vector is greater than the length of the vector registers -> need multiple loads (strip mining)
    - Computations that have both vector and scalar components
        - Scalar components are non-vectorizable
        - Need to separate scalar from vector computations

- Vectorizable Code Characteristics
    - Vectorization can only be done within a do/for loop (innermost)
    - Need enough iterations in the DO loop to offset the start-up time overhead
    - Compiler needs to be able to vectorize code

- Vector Mask
    - Vector mask register - one bit for each element in the vector register
        - Used to mask off elements in the vector register for a subsequent vector copy or store
        - Bits can be set based on a conditional (i.e. flag all bits in the vector that are > 0)
            - Can be set dynamically during computation

- Vector Disadvantages
    - Do we need vector instructions when we now have multiple cores/processors?
    - High price of on-chip vector memory systems
    - Increased code complexity

Lecture 10 - Multiprocessors
----------------------------
- Term paper grade < 18, can redo with comments
- Lectures 1-3,6 not on the final (Lectures 4,5,7-13 on the final)

- Multiple processors inter connected
    - Good for timesharing
    - Hard to write good concurrent programs; many failures

- Paradigms:
    - Parallel Computing - simultaneous use of multiple processors. All within the same architecture.
    - Distributed Computing - network of processors connected by some medium
    - Concurrent Computing - both of the above

- Parallelism Types
    - Job level
    - Task level
    - ISP/TLP
    - Instruction Level
        - Multiple functional units
    - Arithmetic/bit level
        - Multiple ALU

- Efficiency
    - Speedup factor will never equal the number of processes (communication of overhead)
    - Typically, 80% of the computation is done in 20% of the instructions (other 80% is I/O)

- Limitations of Multiprocessors
    - Legacy code needs to be rewritten/parallelized
    - Retraining programmers to program multicores
        - OS/compiler takes care of it

- Flynn's Hardware Taxonomy
    - Organizing processors into categories
    - Instruction & data streams
        - Single or multiple streams
    - Johnson's Expansion
        - Includes IPC (shared memory/message passing)

    - Single Instruction Stream Single Data Stream (SISD)
        - Uniprocessor
        - Pipelining is applicable
    - Single Instruction Multiple Data (SIMD)
        - Single (main) processor
        - Vector operations are "multiple data"
            - One vector instruction = multiple machine instructions on the data stream
            - Pipelined vector units
    - Multiple Instruction Single Data (MISD)
        - Multiple processors, but single data stream
        - Single data stream operated by multiple instruction streams
    - Multiple Instruction Multiple Data (MIMD)
        - Multiple processors / multi computers

- Processors Couplings
    - How do the processors communicate with each other?
    - Tightly Coupled System
        - Processors communicate in a synchronized fashion
        - Shared memory communication
    - Loosely Coupled System
        - Asynchronous communication
        - Message passing
        - High overhead for data exchanged
            - But, can be used for distributed computing

- Parallelism Granularity
    - Coarse-grained
        - Task broken into pieces which can each be executed on a separate processor
        - Computation/communication ratio is very high
    - Medium-grained
        - Tens to thousands of processors running the same code
        - Higher computation/communication ratio
    - Fine-grained
        - Thousands/millions of small pieces executed by very small, simple processors or pipelines

- Memory Architecture
    - Shared (global) memory
    - Distributed (local, message-passing) memory
        - Memory units associated with each processors (i.e. each processor has local memory)
        - Accessing another processor's memory requires message passing
    - Uniform memory
        - All processors take the same time to reach all memory locations
    - Non-uniform memory (NUMA)

- Interconnection Topologies
    - Shared bus
        - Hardware components share a bus that has address/data/command lines
    - Master device
        - Controls and initiates communication
            - Could be a master processor or a device controller (for example, an IO controller that emits interrupts for a CPU)
    - Slave devices
    - Multiple master buses
        - Gives you more bandwidth

    - Mesh architecture
        - Processors directly connected to neighboring processors (with wrap around)
        - Can also have nodes that manage communication/routing with each processor connected to a node
        - Tree architectures also used

    - Multiport Memory
        - Multiple memory modules, each connected to a CPU
    - Crossbar switch
        - Packet-switch or circuit-switch to access a memory module
    - Butterfly (omega) network
        - Each bit of the destination address used to forward a packet

Lecture 11 - Cache Coherency
----------------------------

- Cache coherency - one processor has updated a value in cache, but the change hasn't been written to memory - so other processors can get out-of-date data.

- To have coherency:
    - Any read must always return the most recent write
        - Difficult to implement
    - Or, any write must be eventually seen by a read
    - And all writes are seen in their proper/sequential order (serialization)

- A solution: when changing a value in the cache (and subsequently memory), signal other cache's to set the same address as invalid

- Coherency Misses
    - True sharing misses
    - False sharing misses

- Defining coherency
    - Preserve program order
        - Same program has the same result when executed sequentially or across processors
    - Coherent view of memory
    - Write serialization
        - Two writes to the same location by any 2 processors are seen in the same order

- Write Update protocol
   - In a write-update protocol, **every write** in any of the processors needs to be <u>broadcast on the BUS and needs to update memory</u>.
      - The writer broadcasts on the bus. The others observe the bus (snoop) and update their caches. So a single broadcast on the bus (bus activity) can result in a lot of caches doing an update.
          - The broadcast and snooping are done **simultaneously**. A write happens, the value is broadcast and snooped and updated in the other caches. All this counts as 1 BUS access.
          - Read-Misses count as 1 Bus access.

- Write-Invalidate protocol
    - In a write-invalidate protocol when there is an overwrite in the cache with the most up-to-date value for a block, instead of broadcasting the value to the block, all the other core's caches will set the valid bit for that block to 0, so the next read will be a MISS.
    - On a read miss, the cache that missed will send a request to the Bus, which the block with the dirty block is listening to.
        - When the other block sees this request it sends the correct data to the bus, and the cache that had the read miss, updates it's valid bit to 1 and both caches update their respective shared bits to 1.
    - If you overwrite the value in a block that is shared more than once, the
    cache that overwrote the value only broadcasts that everyone else's values are invalid once.  After that all the write updates happen locally.
    - The Disadvantage to the write-invalidate protocol is that there is a miss on all the readers when somebody writes.

   - Write update optimizations
      1. Avoid unnecessary Memory writes
         - Memory can't keep up with all the writes, since in a W-U protocol you need to write to memory and the bus every write,  which makes memory the bottleneck for the system.
         - Add a dirty bit, in addition to the valid bit and tag
            - This dirty bit signifies 2 things:
                1. That memory is not up to date and we need to update it
                2. It also signifies that the processor that has it set to 1 is the only one that has the most up-to-date value
                    - In the situation where a core's cache has the dirty bit set and another core without that value in the cache is trying to read the "up-to-date" value from memory, that processor that is reading does a **snoop read** from the the shared bus
                      - Each time a value that is marked as dirty is updated, it stays in the cache that the value was updated in and that cache has to write it to the bus so that other caches can do a snoop read of the value.
                     - The memory is only updated when when the block in the cache with the dirty bit set is replaced.
                          - **i.e. one write to memory after all updates are made to value.**
                      - One Bus hit when the block is replaced in the cache.
      2. Reduce the number of Bus Writes
           - Now that memory isn't being updated so much, the bus is the one that is seeing every write, so the writes to the bus will become the bottleneck in the system, because every write from every core .
           - Now we add a shared bit, which is set to 1 when a value is shared with other cores.
              - When a core has a value in a block in its cache, which is updated by another core's write, it sets the shared bit to 1, updates its data and lets the other core know to set the dirty and the shared bit to 1.
                  - So now, the cache with the dirty and shared bit set to one, knows that, when it overwrites the previous value for that block, it needs to broadcast that value.
                  - BUT, if the shared bit is set to 0, it doesn't need to broadcast the value or update memory.  But it does need to update the dirty bit, if necessary to 1.


- Snooping Solution
    - An updated value in cache ->
        - Cache sends a message to all other processors' caches to ask if they have the address
            - Caches invalidate their copies in the cache line (clears the valid flag)
        - Requires broadcasting
- Directory
    - On the bus, contains all the cache lines in all the caches and their dirty/valid states
    - Caches "ask" the directory for permission to write to memory
