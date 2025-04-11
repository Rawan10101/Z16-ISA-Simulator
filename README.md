# Z16 Simulator 

The Z16 Simulator is an Instruction Set Simulator that emulates the execution of Z16 machine code.

## 1. Build Instructions
### Prerequisites

- A C compiler (GCC, Clang, or MSVC)
- Git (for version control)
  
### Cloning the Repository
To get the project, clone the repository using Git:
```bash
 git clone https://github.com/CSCE-2303/csce-2303-s25-project-1-binary-trailblazers.git
 cd csce-2303-s25-project-1-binary-trailblazers
```
### Compilation (Using GCC)
To compile the simulator, navigate to the project directory and run:
```bash
 gcc -o rvsim main.c -std=c11
```
## 2. Usage Guidelines
### Running the Simulator
If you're using an IDE like Clion, Visual Studio, or any C-compatible environment, follow these steps:

1. Open the Project: Download and open the project folder in your IDE.
2. Build and Compile: Open main.c and compile the project. Most IDEs have a Build button for this.
3. Run the Simulator: Press Run (or use the "Run" button in your IDE). This will execute the simulator and it will prompt you for the machine code file.\

If using GCC from the command line, navigate to the project directory and compile with:
```bash
 gcc -o rvsim main.c -std=c11
```
Then, run the simulator using:
```bash
 ./rvsim <machine_code_file_name>
```
Example:
```bash
 ./rvsim program.bin
```
The simulator reads program.bin, disassembles its instructions into human-readable assembly, executes them, and prints the results to the terminal.

## 3. Design Overview
### Core Components
- **Instruction Decoder & Executor:**  
  The core functionality for decoding and executing Z16 instructions is implemented directly within `main.c`. The instruction decoder translates each 16-bit binary instruction into human-readable assembly code, and the executor simulates the behavior of the CPU by performing operations on registers and memory.

  The main responsibilities of this component include:
  - Decoding binary instructions into their respective opcodes and operands.
  - Executing arithmetic, logical, load/store, jump, and branch instructions.
  - Handling system calls (such as `ecall`) for printing integers, strings, and terminating execution.
  - Updating registers and memory based on the instruction results.

- **Memory & Register Management:**  
  The simulator emulates the CPU's memory and registers. It simulates the Z16’s 64 KB of memory and the eight 16-bit registers (x0 to x7). All instructions operate on these registers and memory.

  The memory management system handles:
  - Storing and retrieving data in memory.
  - Simulating the `LB`, `LW`, `SB`, and `SW` load/store operations.
  
  The register management handles:
  - Keeping track of register values.
  - Performing operations on registers for arithmetic and logical instructions.
  - Managing the stack pointer (sp), return address (ra), and function arguments/returns (a0, a1).

### Supported Instructions

The Z16 Instruction Set Simulator supports the following instruction types:

1. **R-Type (Register-to-Register)**  
   - Example Instructions: `ADD`, `SUB`, `AND`, `OR`, `XOR`, `SLL`, `SRL`, `SRA`, `SLT`, `SLTU`, `MV`, `JR`, `JALR`  
   - Format: `funct4 | rs2 | rs1 | funct3 | opcode`  
   - Operation: `rs1 = rs1 op rs2`

2. **I-Type (Immediate)**  
   - Example Instructions: `ADDI`, `SLTI`, `ORI`, `ANDI`, `SLLI`, `SRLI`, `SRAI`, `LI`  
   - Format: `imm[6:0] | rs1 | funct3 | opcode`  
   - Operation: `rs1 = rs1 op imm`

3. **B-Type (Branch)**  
   - Example Instructions: `BEQ`, `BNE`, `BZ`, `BNZ`, `BLT`, `BGE`, `BLTU`, `BGEU`  
   - Format: `imm[4:1] | rs2 | rs1 | funct3 | opcode`  
   - Operation: Compares `rs1` with `rs2` (or zero for `BZ` and `BNZ`) and jumps if the condition is met.

4. **S-Type (Store)**  
   - Example Instructions: `SB`, `SW`  
   - Format: `imm[3:0] | rs2 | rs1 | funct3 | opcode`  
   - Operation: Stores data from `rs2` to memory at an address specified by `rs1` and the immediate.

5. **L-Type (Load)**  
   - Example Instructions: `LB`, `LW`, `LBU`  
   - Format: `imm[3:0] | rs2 | rd | funct3 | opcode`  
   - Operation: Loads data from memory into register `rd`.

6. **J-Type (Jump)**  
   - Example Instructions: `J`, `JAL`  
   - Format: `f | imm[9:4] | rd | imm[3:1] | opcode`  
   - Operation: Jumps to an address specified by the immediate value and register.

7. **U-Type (Upper Immediate)**  
   - Example Instructions: `LUI`, `AUIPC`  
   - Format: `f | imm[15:10] | rd | imm[9:7] | opcode`  
   - Operation: Loads a large immediate into the upper 10 bits of the register.

8. **SYS-Type (System)**  
   - Example Instruction: `ECALL`  
   - Format: `service[11:0] | funct3 | opcode`  
   - Operation: Performs system calls, such as printing integers or strings and terminating the program.

### Special Semantics
- **Arithmetic/Logical Operations:**  
  All arithmetic and logical instructions use the two-register format where `rs1 = rs1 op rs2`.
  
- **Jump Instructions (JR & JALR):**  
  Jump instructions like `JR` and `JALR` jump to an address specified by the contents of a register (`rs2`), rather than a constant immediate address.

- **Jumps & Branches:**  
  All jumps and branches are PC-relative, meaning the target address is calculated relative to the current program counter (PC).

- **Load/Store Instructions:**  
  For load/store instructions, the memory address is calculated using a base address (`rs1`) and an offset (the immediate value).

- **System Calls (`ECALL`):**  
  System calls are handled by the `ECALL` instruction. This supports operations like printing integers, printing strings, and terminating the program.
  
### Register ABI
- **x0**: `t0` (Temporary register)
- **x1**: `ra` (Return address)
- **x2**: `sp` (Stack pointer)
- **x3**: `s0` (Callee-saved register)
- **x4**: `s1` (Callee-saved register)
- **x5**: `t1` (Temporary register)
- **x6**: `a0` (Function argument / return value)
- **x7**: `a1` (Function argument / return value)

### Memory & Stack Management
The Z16 simulator has:
- A fixed 64KB memory space that can be accessed via load/store instructions.
- Stack pointer management with a stack-based calling convention using `sp` (x2).
- Arguments passed via registers `a0` and `a1` (x6, x7), and the return address stored in `ra` (x1).


## 4. Project Challenges & Limitations

- Handling the unsigned and signed immediate values correctly.
- ecall execution.
- Building the test cases to cover all the instructions correctly.

Implementation of the disassembling and execution code of the simulator was not that challenging. However, the main challenge was getting the correct input from the assembler Z16 to the simulator.

### Limitations of the assembler were:
- Incorrect encoding of j-type instructions.
- not differentiating between auipc and lui instructions.
- The infinite loop due to an error in the e-call implementation.
- Branch instruction limited range for branching.
- limited range of the offset of the storing instructions.
- .data section incorrect assign to the addressess.

## 5. Extra features

- The choice of printing diretly the output or previwing the instructions with the output.
- Detecting error when there is inifite calls.
  
 
