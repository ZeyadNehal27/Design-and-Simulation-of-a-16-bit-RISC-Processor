# Design-and-Simulation-of-a-16-bit-RISC-Processor
This is basically a single cycle 16 bit RISC Processor. It goes through five stages.
ABSTRACT

This presents the complete design and behavioral simulation of a 16-bit Reduced Instruction Set Computer (RISC) processor using Verilog Hardware Description Language (HDL). The processor is designed around a classic five-stage pipeline architecture comprising Instruction Fetch (IF), Instruction Decode (ID), Execute (EX), Memory Access (MEM), and Write-Back (WB) stages. The design integrates a 16-bit Arithmetic Logic Unit (ALU), a register file containing eight 16-bit general-purpose registers, a program counter (PC), an instruction memory (IMEM), and a data memory (DMEM). The complete RTL (Register Transfer Level) design is implemented and simulated using Xilinx Vivado 2025.2, and behavioral simulation waveforms are analyzed to verify correct datapath operation. The simulation results confirm that the processor executes arithmetic, logical, load/store, and branch instructions correctly, with the register file updating as expected at each clock cycle. The design provides a foundation for understanding the internal micro-architecture of modern RISC-based embedded processors.





Keywords: RISC Processor, Verilog HDL, 16-bit, Five-Stage Pipeline, ALU, Register File, Xilinx Vivado, Behavioral Simulation, RTL Design.


INDEX
Chapter 1   –   Introduction
1.1   –   Overview of RISC Architecture
1.2   –   Need for a Custom 16-Bit RISC Processor
1.3   –   Objectives of the Project
1.4   –   Tools and Technologies Used

Chapter 2   –   Architecture and Design
2.1   –   Processor Architecture Overview
2.2   –   Instruction Set Architecture (ISA)
2.3   –   Datapath Design
2.4   –   Control Unit Design
2.5   –   Block Diagram

Chapter 3   –   Verilog HDL Module Descriptions
3.1   –   Top-Level Module (risc_processor_top)
3.2   –   Program Counter (PC)
3.3   –   Instruction Memory (IMEM)
3.4   –   Control Unit (CU)
3.5   –   Register File (RF)
3.6   –   Arithmetic Logic Unit (ALU)
3.7   –   Data Memory (DMEM)

Chapter 4   –   Simulation and Results
4.1   –   Testbench Description
4.2   –   Simulation Waveform Analysis
4.3   –   Verification of Results

Chapter 5   –   Advantages and Limitations

Chapter 6   –   Future Scope and Conclusion
References
CHAPTER 1: INTRODUCTION
1.1 Overview of RISC Architecture
Reduced Instruction Set Computing (RISC) is a processor design philosophy that favors a small, highly optimized set of instructions rather than the complex, variable-length instructions found in Complex Instruction Set Computing (CISC) architectures. The fundamental principle of RISC is that a simpler instruction set can be executed more efficiently by the hardware, allowing the processor to achieve higher clock speeds and shorter execution times per instruction [1]. RISC architectures typically execute one instruction per clock cycle, employ fixed-length instruction formats, use a load/store architecture in which only explicit load and store instructions interact with memory, and maintain a large file of general-purpose registers to minimize memory accesses [1,2].
The origins of the RISC philosophy are attributed to John Cocke at IBM in the late 1970s, with foundational academic work contributed by David Patterson at the University of California, Berkeley, and John Hennessy at Stanford University [2]. These principles led to the development of processor families such as MIPS, SPARC, ARM, and the widely adopted RISC-V, all of which are central to modern embedded systems, mobile devices, and high-performance computing environments [3].
1.2 Need for a Custom 16-Bit RISC Processor
While commercial RISC processors offer remarkable performance, their internal architectures are highly complex and proprietary. For educational and research purposes, designing a simplified 16-bit RISC processor from scratch in Verilog HDL provides a transparent and accessible means of understanding the fundamental principles of digital system design, pipelining, and microprocessor organization [4]. A 16-bit word width is a practical choice for a teaching model as it reduces the complexity of routing and memory while retaining sufficient expressiveness to implement meaningful instruction types, including arithmetic operations, logical operations, load/store memory interactions, and conditional branching [4,5].
Furthermore, Verilog HDL allows the designer to describe the processor at the Register Transfer Level (RTL), bridging the conceptual gap between the logic gate level and the software programming abstraction. Simulation tools such as Xilinx Vivado provide behavioral waveform outputs that allow every internal signal transition to be observed, making the design highly verifiable [5].
1.3 Objectives of the Project
The primary objectives of this project are as follows. First, to design a five-stage pipelined 16-bit RISC processor fully described in Verilog HDL. Second, to implement key components including the program counter, instruction memory, control unit, register file, ALU, and data memory as individual Verilog modules. Third, to write a comprehensive testbench to simulate the processor and load a meaningful sequence of instructions into the instruction memory. Fourth, to analyze the behavioral simulation waveforms produced by Xilinx Vivado 2025.2 to verify the correctness of the datapath and control logic. Fifth, to provide a complete technical reference for the design that can serve as a foundation for future enhancements such as hazard detection, forwarding units, and cache integration.
1.4 Tools and Technologies Used
The design and simulation of the 16-bit RISC processor were carried out using Xilinx Vivado Design Suite 2025.2, which provides an integrated environment for RTL design entry, functional simulation, synthesis, and implementation targeting Xilinx FPGAs. The hardware description is written entirely in Verilog HDL using the IEEE 1364-2001 standard. The simulation is a purely behavioral functional simulation, meaning that it verifies the logical correctness of the design without taking into account gate delays or physical routing constraints. The host system ran on Windows 11, with the Vivado simulator (xsim) serving as the waveform generation and analysis tool [5].




















CHAPTER 2: ARCHITECTURE AND DESIGN
2.1 Processor Architecture Overview
The designed processor follows a classical five-stage pipeline architecture, which is the most widely documented model in computer architecture literature [1,2]. Each stage of the pipeline operates on a different instruction simultaneously, allowing up to five instructions to be in flight within the processor at any given time in an ideal scenario. The five stages are Instruction Fetch (IF), Instruction Decode (ID), Execute (EX), Memory Access (MEM), and Write-Back (WB). The current implementation uses a single-cycle behavioral model for simulation purposes, which is functionally equivalent to observing one complete pipeline traversal per instruction without the inter-stage pipeline registers, making the simulation output straightforward to interpret [4].
The processor operates on a 16-bit datapath. All general-purpose registers are 16 bits wide. The instruction word is also 16 bits, which constrains the instruction format and the range of immediate values that can be encoded. The program counter is a 16-bit register that addresses a 32-word instruction memory, and the data memory contains 16 addressable locations of 16-bit width, which is sufficient for testbench verification purposes [5].
2.2 Instruction Set Architecture (ISA)
The instruction set is custom-designed and encoded within a 16-bit word. The opcode occupies the most significant 4 bits [15:12], providing 16 possible instruction types. The source register fields occupy bits [11:9] and [8:6], and the destination register field occupies bits [5:3], each being 3 bits wide to address one of eight registers (R0 through R7). The remaining 3 bits [2:0] may encode a function code or an immediate value depending on the instruction type [4,5].
The supported instruction types are as follows. ADD computes the sum of two source registers and stores the result in the destination register. SUB computes the difference. AND and OR perform bitwise logical operations. LOAD reads a 16-bit value from data memory at the address specified by a source register into the destination register. STORE writes the value of a source register into data memory at the address given by another source register. BEQ performs a branch if two source registers are equal, modifying the program counter. NOP performs no operation and is used for pipeline padding in the testbench [3,4].
2.3 Datapath Design
The datapath of the processor consists of a series of functional units interconnected by 16-bit buses. The program counter provides the current instruction address to the instruction memory, which outputs the 16-bit instruction word. The instruction is then decoded by the control unit, which generates the requisite control signals: reg_write, mem_read, mem_write, alu_src, and branch. The register file reads up to two source operands based on the decoded register address fields. The ALU receives these operands, or an operand and an immediate value in the case of certain instruction types, and produces a 16-bit result along with a zero flag. The data memory is accessed when a load or store instruction is decoded, and the result is routed back to the register file during the write-back stage [2,4].
2.4 Control Unit Design
The control unit is a combinational block that takes the 4-bit opcode as input and generates the set of control signals that direct the operation of the datapath. It is implemented as a case statement in Verilog, which maps each opcode to its corresponding control word [3]. The control signals determine whether the register file is written (reg_write), whether the memory is read (mem_read) or written (mem_write), whether the second ALU input comes from a register or an immediate value (alu_src), and whether a branch condition should be evaluated (branch). The ALU operation code (alu_op) is a 2-bit signal derived from the opcode that instructs the ALU to perform addition, subtraction, or a logical operation [4,5].
2.5 Block Diagram
The following block diagram illustrates the high-level architecture of the 16-bit RISC processor, showing the five pipeline stages and the primary functional sub-modules within each stage. The dataflow proceeds from left to right, beginning at the Instruction Fetch stage and terminating at the Write-Back stage.






Instruction Fetch (IF)
→
Instruction Decode (ID)
→
Execute (EX)
→
Memory Access (MEM)
→
Write Back (WB)



↓

↓

↓

↓

↓



PC IMEM

Control Unit (CU) Reg File (RF)

ALU 16-bit

DMEM Data Bus

Register File (RF)



Fig. 2.1: Block Diagram of the 16-Bit RISC Processor (Five-Stage Pipeline)

As shown in Fig. 2.1, the Instruction Fetch stage houses the Program Counter (PC) and the Instruction Memory (IMEM). The Instruction Decode stage contains the Control Unit (CU) and the Register File (RF), which are responsible for interpreting the instruction opcode and reading the required operands. The Execute stage contains the 16-bit ALU, which performs all arithmetic and logical computations. The Memory Access stage manages reads from and writes to the Data Memory (DMEM). Finally, the Write-Back stage routes either the ALU result or the memory read data back into the Register File [1,2].




CHAPTER 3: VERILOG HDL MODULE DESCRIPTIONS
3.1 Top-Level Module (risc_processor_top)
The top-level module, named risc_processor_top, instantiates all sub-modules and wires them together according to the datapath described in Chapter 2. It declares the primary inputs as the system clock (clk) and synchronous reset (rst), and its internal wires carry all inter-module signals including the instruction bus, register addresses, ALU operands, and memory data. This module is the direct target of the simulation testbench [5].
3.2 Program Counter (PC)
The program counter module (program_counter) is a synchronous 16-bit register. On the rising edge of the clock, if the reset signal is asserted, the PC is cleared to zero; otherwise, it is updated to the next program counter value (pc_next) computed by the datapath. In a non-branching instruction, pc_next equals the current PC value plus one (word-addressed). In a branching instruction where the branch condition is met, pc_next is set to the branch target address [4].
3.3 Instruction Memory (IMEM)
The instruction memory module (instruction_memory) is modeled as a 32x16 synchronous ROM. It is initialized in the simulation testbench using the Verilog initial block, which loads a sequence of test instructions into the memory array. On each clock cycle, the memory outputs the 16-bit instruction word located at the address provided by the program counter. In a physical implementation, this module would map to a block RAM resource on an FPGA [5].



3.4 Control Unit (CU)
The control unit module (control_unit) is a purely combinational block implemented using a Verilog case statement. It accepts the 4-bit opcode extracted from bits [15:12] of the instruction and produces the following output control signals: reg_write (1-bit), mem_read (1-bit), mem_write (1-bit), alu_src (1-bit), branch (1-bit), and alu_op (2-bit). For undefined or unused opcodes, the control unit defaults all outputs to zero, ensuring safe behavior [3,4].
3.5 Register File (RF)
The register file module (register_file) implements eight 16-bit general-purpose registers, addressed as R0 through R7. The module supports two simultaneous asynchronous reads and one synchronous write. On each rising clock edge, if the reg_write control signal is asserted and the write register address is nonzero (to protect R0 from being overwritten), the write data is latched into the specified register. The module is central to the processor's operation, as every computational result is eventually stored in one of its registers [2,4].
3.6 Arithmetic Logic Unit (ALU)
The ALU module (alu) performs all arithmetic and logical computations on 16-bit operands. It is a purely combinational block controlled by the 2-bit alu_op signal. When alu_op is 2'b00, the ALU computes the sum of its two inputs. When alu_op is 2'b01, it computes the difference. When alu_op is 2'b10, it computes the bitwise AND. When alu_op is 2'b11, it computes the bitwise OR. The module also produces a 1-bit zero flag, which is asserted when the ALU result is equal to zero, and is used by the branch logic to determine whether a conditional branch should be taken [3,5].
3.7 Data Memory (DMEM)
The data memory module (data_memory) is implemented as a 16x16 synchronous RAM. It supports a single asynchronous read port and a synchronous write port. When the mem_read signal is asserted, the data at the address specified by the ALU result is placed on the read data bus. When the mem_write signal is asserted, the value of the second source register is written to the address specified by the ALU result on the next rising clock edge [4,5].

