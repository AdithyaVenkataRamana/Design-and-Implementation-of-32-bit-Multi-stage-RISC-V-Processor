# Design-and-Implementation-of-32-bit-Multi-stage-RISC-V-Processor
-------------------------------------------------

<details>
<summary><b>1. Why Choose a Multi-Stage Processor Over a Single-Cycle Processor?</b> </summary>
  
**i. Single Cycle Processor**

- Before diving into the multi-stage pipeline processor, let's first understand the singlecycle processor. Then we can see why pipelining is important.
<img width="747" height="244" alt="Image" src="https://github.com/user-attachments/assets/b6fbe355-b7ff-4f13-a39d-e4f6676bc349" />

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

<img width="744" height="283" alt="Image" src="https://github.com/user-attachments/assets/f42cb67a-c48a-4520-abdb-77796764d357" />
- No need to handle data, control, or structural hazards since there’s no overlap between
instructions.
- The clock cycle has to be long enough to finish the slowest instruction so faster
instructions waste time.
- Only one instruction runs at a time, so it’s slow overall.


</details>
---------------------------------------------------
<details>
<summary><b>2. Why Choose a Multi-Stage Processor Over a Single-Cycle Processor?</b></summary>

- That's why we use a multi-stage processor, it runs faster and is more efficient than
a single-cycle processor.

- Stages in Multi stage pipelined processor
  ## stage pipeline:
IF → ID → EX → MEM → WB




### IF – Instruction Fetch:
- Here we fetch an instruction from memory.
- PC register already contains the address of next instruction, so simply whatever is there
in PC from that memory location we read.
### ID – Instruction Decode:
- Here we try to decode the opcode and find out the what kind of instruction it is.
- While decoding is going on it also do some fetching.
- Assuming that there will be 16bit immediate data, it will be taking that last 16bit of
instruction and it will be doing a sign extension to 32bits.
### EX-Execute:
- Here we execute the instruction or some instructions we have to compute the effective
address.
- It’s actual memory address from which data will be loaded (LW) or to which data will
be stored(SW).
### MEM – Memory Access:
- In this stage here it actually d memory access, read & write from memory.
- For branch instruction it decides whether to branch or not.
### WB – Write Back:
 The result of an instruction is written back to the register file.
- After an instruction finishes calculating, we store the result into register in the register
file. 

- Let us understand the pipeline stages in a multi-stage processor by taking an example. 
  **Program:**

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



<img width="688" height="507" alt="Image" src="https://github.com/user-attachments/assets/e7445b82-0587-4c2b-9583-be36296b104f" />
</details>
---------------------------------------------------

<details>
<summary><b></b> 3. Micro-Operations in Each Pipeline Stage</summary> 

<img width="1908" height="593" alt="Image" src="https://github.com/user-attachments/assets/ac1e4b32-6259-4dcd-ba41-fbefe5837ded" />
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
---------------------------------------------------

<details>
<summary><b></b>4. Types of Hazards in a Multi-Stage Pipeline </summary> 


## Data Hazards:
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

<img width="936" height="627" alt="Image" src="https://github.com/user-attachments/assets/81460f42-11a3-4960-9fec-63c37ab26967" />

- add x3, x1, x2 is trying to read x1 and x2 in its ID stage.
- But x1 and x2 haven’t reached WB yet, so their correct values aren't available yet.
- This is a Read After Write (RAW) data hazard.

## Control Hazards:
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

- **Demo Video**
[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1IAJcRL9DWJ0aPErHSCn9yp1pkTqZmGHF/view?usp=drive_link))

<img width="1019" height="484" alt="Image" src="https://github.com/user-attachments/assets/4e974507-baf8-4a1f-bd46-e822242aaf4c" />
- In a 5-stage pipeline (like in Ripes), branch instructions like beq are only
resolved in the Execute (EX) stage, which is 2 cycles after the fetch.
- The branch decision (beq) is only made in the Execute (EX) stage.
- Meanwhile, the next instructions (addi x3, addi x4) are already fetched and
possibly entered decode or execute stages.
- This creates a Control Hazard — the CPU is unsure whether to continue with
x3/x4 or jump to target. 

## Structural Hazard:
A Structural Hazard occurs when hardware resources are not sufficient to support
multiple instructions executing in parallel in the pipeline. 

- Example:

```
lw x1, 0(x2) # Instruction 1 — Load word from memory into x1
addi x3, x0, 5 # Instruction 2 — Set x3 = 5 (uses ALU, no memory access)
sw x4, 0(x5) # Instruction 3 — Store word from x4 into memory at address in x5 

```

- **Demo Video**
[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1V3A_KQz1bCuY_dOkxBQSieeDTePHF8T/view?usp=drive_link))
- lw x1, 0(x2) and sw x4, 0(x5) involve memory access.
- If memory is not properly initialized or x2/x5 don't point to valid memory,
these memory-related instructions don't actually read or write correctly.
- But addi x3, x0, 5 is a pure ALU instruction (doesn't depend on memory),
so it always works and updates x3.

</details>
--------------------------------------------------

<details>
<summary><b>5. Implementation of Multi stage RISC-V processor using Verilog</b></summary>
  
- Design:
  
```
module pipe_riscv32(clk1, clk2);
 input clk1, clk2;
 reg [31:0] pc, IF_ID_IR, IF_ID_NPC;
 reg [31:0] ID_EX_IR, ID_EX_NPC, ID_EX_A, ID_EX_B, ID_EX_Imm;
 reg [2:0] ID_EX_type, EX_MEM_type, MEM_WB_type;
 reg [31:0] EX_MEM_IR, EX_MEM_ALUOut, EX_MEM_B;
 reg EX_MEM_cond;
 reg [31:0] MEM_WB_IR, MEM_WB_ALUOut, MEM_WB_LMD;
 reg [31:0] Reg [0:31]; // Register Bank 32x32
 reg [31:0] MEM [0:1023]; // Memory 1024x32
 reg HALTED;
 reg TAKEN_BRANCH;
 parameter ADD = 6'b000000,
 SUB = 6'b000001,
 AND = 6'b000010,
 OR = 6'b000011,
 SLT = 6'b000100,
 MUL = 6'b000101,
 HLT = 6'b111111,
 LW = 6'b001000,
 SW = 6'b001001,
 ADDI= 6'b001010,
 SUBI= 6'b001011,
 SLTI= 6'b001100,
 BNEQZ=6'b001101,
 BEQZ= 6'b001110;
 parameter RR_ALU = 3'b000,
 RM_ALU = 3'b001,
 LOAD = 3'b010,
 STORE = 3'b011,
 BRANCH = 3'b100,
 HALT = 3'b101;
 // IF Stage
 always @(posedge clk1)
 if (HALTED == 0) begin
 if (((EX_MEM_IR[31:26] == BEQZ) && (EX_MEM_cond == 1)) ||
 ((EX_MEM_IR[31:26] == BNEQZ) && (EX_MEM_cond == 0))) begin
 IF_ID_IR <= #2 MEM[EX_MEM_ALUOut];
 TAKEN_BRANCH <= #2 1'b1;
 IF_ID_NPC <= #2 EX_MEM_ALUOut + 1;
 pc <= #2 EX_MEM_ALUOut + 1;
 end else begin
 IF_ID_IR <= #2 MEM[pc];
 IF_ID_NPC <= #2 pc + 1;
 pc <= #2 pc + 1;
 end
 end
 // ID Stage
 always @(posedge clk2)
 if (HALTED == 0) begin
 ID_EX_A <= #2 Reg[IF_ID_IR[25:21]];
 if (IF_ID_IR[20:16] == 5'b00000)
 ID_EX_B <= #2 0;
 else
 ID_EX_B <= #2 Reg[IF_ID_IR[20:16]];
 ID_EX_NPC <= #2 IF_ID_NPC;
 ID_EX_IR <= #2 IF_ID_IR;
 ID_EX_Imm <= #2 {{16{IF_ID_IR[15]}}, IF_ID_IR[15:0]}; // sign-extend imm
 case (IF_ID_IR[31:26])
 ADD, SUB, AND, OR, SLT, MUL: ID_EX_type <= #2 RR_ALU;
 ADDI, SUBI, SLTI: ID_EX_type <= #2 RM_ALU;
 LW: ID_EX_type <= #2 LOAD;
 SW: ID_EX_type <= #2 STORE;
 BNEQZ, BEQZ: ID_EX_type <= #2 BRANCH;
 HLT: ID_EX_type <= #2 HALT;
 default: ID_EX_type <= #2 HALT;
 endcase
 end
 // EX Stage
 always @(posedge clk1)
 if (HALTED == 0) begin
 EX_MEM_type <= #2 ID_EX_type;
 EX_MEM_IR <= #2 ID_EX_IR;
 TAKEN_BRANCH <= #2 0;
 case (ID_EX_type)
 RR_ALU: begin
 case (ID_EX_IR[31:26])
 ADD: EX_MEM_ALUOut <= #2 ID_EX_A + ID_EX_B;
 SUB: EX_MEM_ALUOut <= #2 ID_EX_A - ID_EX_B;
 AND: EX_MEM_ALUOut <= #2 ID_EX_A & ID_EX_B;
 OR: EX_MEM_ALUOut <= #2 ID_EX_A | ID_EX_B;
 MUL: EX_MEM_ALUOut <= #2 ID_EX_A * ID_EX_B;
 SLT: EX_MEM_ALUOut <= #2 (ID_EX_A < ID_EX_B);
 default: EX_MEM_ALUOut <= #2 32'hxxxxxxxx;
 endcase
 end
 RM_ALU: begin
 case (ID_EX_IR[31:26])
 ADDI: EX_MEM_ALUOut <= #2 ID_EX_A + ID_EX_Imm;
 SUBI: EX_MEM_ALUOut <= #2 ID_EX_A - ID_EX_Imm;
 SLTI: EX_MEM_ALUOut <= #2 (ID_EX_A < ID_EX_Imm);
 default: EX_MEM_ALUOut <= #2 32'hxxxxxxxx;
 endcase
 end
 LOAD, STORE: begin
 EX_MEM_ALUOut <= #2 ID_EX_A + ID_EX_Imm;
 EX_MEM_B <= #2 ID_EX_B;
 end
 BRANCH: begin
 EX_MEM_ALUOut <= #2 ID_EX_NPC + ID_EX_Imm;
 EX_MEM_cond <= #2 (ID_EX_A == 0); // assuming zero check for branch
 end
 endcase
 end
 // MEM Stage
 always @(posedge clk2)
 if (HALTED == 0) begin
 MEM_WB_type <= #2 EX_MEM_type;
 MEM_WB_IR <= #2 EX_MEM_IR;
 case (EX_MEM_type)
 RR_ALU, RM_ALU: MEM_WB_ALUOut <= #2 EX_MEM_ALUOut;
 LOAD: MEM_WB_LMD <= #2 MEM[EX_MEM_ALUOut];
 STORE:
 if (TAKEN_BRANCH == 0)
 MEM[EX_MEM_ALUOut] <= #2 EX_MEM_B;
 endcase
 end
 // WB Stage
 always @(posedge clk1)
 if (HALTED == 0) begin
 if (TAKEN_BRANCH == 0)
 case (MEM_WB_type)
 RR_ALU: Reg[MEM_WB_IR[15:11]] <= #2 MEM_WB_ALUOut;
 RM_ALU, LOAD: Reg[MEM_WB_IR[20:16]] <= #2 (MEM_WB_type == LOAD
? MEM_WB_LMD : MEM_WB_ALUOut);
 HALT: HALTED <= #2 1'b1;
 endcase
 end
endmodule 
```

- Test Bench:

 ```
module pipe_riscv32_tb;
 reg clk1, clk2;
 integer i;
 // Instantiate the processor
 pipe_riscv32 DUT(clk1, clk2);
 // Clock generation
 initial begin
 clk1 = 0; clk2 = 0;
 forever begin
 #5 clk1 = ~clk1; // Toggle clk1 every 5 time units
 #5 clk2 = ~clk2; // Toggle clk2 after clk1
 end
 end
 // Initialize memory and registers
 initial begin
 // Clear register file and memory
 for (i = 0; i < 32; i = i + 1)
 DUT.Reg[i] = 0;
 for (i = 0; i < 1024; i = i + 1)
 DUT.MEM[i] = 32'h00000000;
 // -----------------------------
 // Program: Simple instruction flow
 // -----------------------------
 // ADDI R1, R0, #5 => R1 = 5
 // ADDI R2, R0, #10 => R2 = 10
 // ADD R3, R1, R2 => R3 = R1 + R2 = 15
 // SW R3, 100(R0) => MEM[100] = R3
 // LW R4, 100(R0) => R4 = MEM[100]
 // HLT
 DUT.MEM[0] = {6'b001010, 5'd0, 5'd1, 16'd5}; // ADDI R1, R0, 5
 DUT.MEM[1] = {6'b001010, 5'd0, 5'd2, 16'd10}; // ADDI R2, R0, 10
 DUT.MEM[2] = {6'b000000, 5'd1, 5'd2, 5'd3, 11'd0}; // ADD R3, R1, R2
 DUT.MEM[3] = {6'b111111, 26'd0}; // HLT
 // Reset control flags
 DUT.HALTED = 0;
 DUT.TAKEN_BRANCH = 0;
 DUT.pc = 0;
 // Simulation time
 #200;
 // Output Register Contents
 $display("\nFinal Register Values:");
 for (i = 0; i < 8; i = i + 1)
 $display("R[%0d] = %0d", i, DUT.Reg[i]);
 $display("\nMemory[100] = %0d", DUT.MEM[100]);
 $finish;
 end
endmodule 
```
</details>

--------------------------------------------
<details>
<summary><b>6. Result</b></summary>
Final Register Values:
R[0] = 0
R[1] = 5
R[2] = 10
R[3] = 5
R[4] = 0
R[5] = 0
R[6] = 0
R[7] = 0
Memory[100] = 0
testbench.sv:56: $finish called at 200 (1s) 
  <img width="1814" height="783" alt="Image" src="https://github.com/user-attachments/assets/45edf2a0-daad-4e7d-91bf-d30bb0e1ee0c" />
</details>
