# Design-and-Implementation-of-32-bit-Multi-stage-RISC-V-Processor
-------------------------------------------------

<details>
<summary><b>1. Why Choose a Multi-Stage Processor Over a Single-Cycle Processor?:</b> 1</summary>
  
**i. Single Cycle Processor**

- Before diving into the multi-stage pipeline processor, let's first understand the singlecycle processor. Then we can see why pipelining is important.
-  [  Image]

-  A Single Cycle RISC-V Processor is a basic CPU design in which every
instruction is executed in exactly one clock cycle.
- This includes all five stages of instruction execution: instruction fetch, decode,
execute, memory access, and write-back.



- Program:

```
main:
addi x1, x0, 5
addi x2, x0, 10
add x3, x2, x1
```

- **Demo Video**

[![Watch the video](https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_)

- [Image]
- No need to handle data, control, or structural hazards since there’s no overlap between
instructions.
- The clock cycle has to be long enough to finish the slowest instruction so faster
instructions waste time.
- Only one instruction runs at a time, so it’s slow overall.


</details>
-------------------------------------------------
<details>
<summary><b>2. Why Choose a Multi-Stage Processor Over a Single-Cycle Processor?</b></summary>

- That's why we use a multi-stage processor, it runs faster and is more efficient than
a single-cycle processor.

- Stages in Multi stage pipelined processor
 **5-stage pipeline:**
IF → ID → EX → MEM → WB




- **IF – Instruction Fetch:**
- Here we fetch an instruction from memory.
- PC register already contains the address of next instruction, so simply whatever is there
in PC from that memory location we read.
- **ID – Instruction Decode:**
- Here we try to decode the opcode and find out the what kind of instruction it is.
- While decoding is going on it also do some fetching.
- Assuming that there will be 16bit immediate data, it will be taking that last 16bit of
instruction and it will be doing a sign extension to 32bits.
- **EX-Execute:**
- Here we execute the instruction or some instructions we have to compute the effective
address.
- It’s actual memory address from which data will be loaded (LW) or to which data will
be stored(SW).
- **MEM – Memory Access:**
- In this stage here it actually d memory access, read & write from memory.
- For branch instruction it decides whether to branch or not.
- **WB – Write Back:**
 The result of an instruction is written back to the register file.
- After an instruction finishes calculating, we store the result into register in the register
file. 

- Let us understand the pipeline stages in a multi-stage processor by taking an example. 
- Program:

```
text
main:
addi x1, x0, 5
addi x2, x0, 10
nop
nop
add x3, x2, x1 
```

- **Demo Video**

[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1kfnzHK05PBeWIAu1yKHBBrIyfgy1o7B-%20/view?usp=drive_link))



- [Image]
</details>
-------------------------------------------------

<details>
<summary><b></b> 3. Micro-Operations in Each Pipeline Stage</summary> 

 [Image]
- **IF_ID Stage:**
  The instruction is fetched from memory using the program counter, and the PC is
incremented by 4 to point to the next instruction.
- **ID/EX Stage :**
  The instruction is decoded, source registers are read, and control signals are generated
for the next stage.
- **EX/MEM Stage:**
  The ALU performs the required operation such as arithmetic or address calculation, and
the result is passed to the memory stage along with updated control signals.

- **MEM/WB Stage:**
  If it’s a load instruction, data is read from memory; otherwise, the ALU result is
prepared to be written back to the register file.

</details>
-------------------------------------------------

<details>
<summary><b></b>4. Types of Hazards in a Multi-Stage Pipeline </summary> 

  
- **Data Hazards:**
When an instruction depends on the result of a previous instruction that hasn’t yet
completed.

Example:
```
addi x1, x0, 5 # x1 = 5
addi x2, x0, 10 # x2 = 10
add x3, x1, x2 # x3 = x1 + x2 → data hazard here 

```

**Demo Video**
[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1hl8igFd6qln0DeALkVckzusGewKk0ejF/view?usp=drive_link))

-[Image]

- add x3, x1, x2 is trying to read x1 and x2 in its ID stage.
- But x1 and x2 haven’t reached WB yet, so their correct values aren't available yet.
- This is a Read After Write (RAW) data hazard.

- **Control Hazards:**
Hazards caused by branch or jump instructions that change the program counter
(PC). 
- Example:
  
 - What's is hazard in this
- Assume x1 = 5, x2 = 5 initially
```
addi x1, x0, 5 # x1 = 5
addi x2, x0, 5 # x2 = 5
beq x1, x2, target # If equal, jump to target
addi x3, x0, 10 # This should be skipped if branch is taken
addi x4, x0, 20 # This will be target
target:
addi x5, x0, 30 # This is where we land if beq taken 
```


