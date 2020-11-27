# Physical-Design-Openlane-Flow
RTL to GDS flow using Openlane tool flow.


# IC Design Components and Terminologies
**IC** or **Integrated circuit** is basically an electronic circuit consisting of large number of transistors, resistors and capacitors etc inside a single semiconductor chip. They come in a variety of packages and sizes. Some of the commonly used ICs include Timer 555 ic, 741 operational amplifiers, 78xx series of voltage regulators and 74xx series of logic gates.<br/>
**SOC - System on Chip** is also a type of IC which has the capabilities of a computer built inside the chip. It typically consists of a CPU, memory , input and output ports, analog IPs and other units integrated within itself.SoCs can be applied to any computing task. They are typically used in mobile computing such as tablets, smartphones, smartwatches and netbooks as well as embedded systems.<br/>
**Core** is the section of the chip where the fundamental logic of the design is placed. **Die**   contains the core, it is the small semiconductor material specimen on which the fundamental circuit is fabricated. IC's are fabricated on a single 9 inch or 12 inch diameter silicon wafer, which contains hundreds of mirror images of the fundamental logic. This wafer is then cut into small pieces, each piece has similar functionality of the fundamental logic called Die. **Pad cells** surround the rectangular metal patches where external bonds are made. input,output and power pad cells are commonly used pad cells.
<p align="center">
<img src="https://github.com/akilm/Physical-Design-Openlane-Flow/blob/main/Images/day%201/ic1.PNG" 
alt="alt text" width = 553 height = 358  >
<p/>

## Package Types ##
Integrated circuits are put into protective packages to allow easy handling and assembly onto printed circuit boards and to protect the devices from damage. A very large number of different types of package exist. Through-hole packages, Surface mount, Chip carrier, Pin grid arrays, Flat packages and ball grid array are some of the most common packages. In this workshop we deal with a variant of Flat package called QFN-48 ( Quad Flat No leads with 48 pins)
<p align="center">
<img src="https://github.com/akilm/Physical-Design-Openlane-Flow/blob/main/Images/day%201/qfn.PNG" 
alt="alt text"  >
<p/>


## Foundry IPs and Macros
PLLs, DAC, ADC and SRAMs come under the category of foundry IP. They have to be manually designed or need some human interference (or intelligence) essentially to define and create them. IPs(Intellectual property) are targeted on a particular technology nodes. Macros are basically digital blocks which are made up of purely digital logic.


# RISC-V
The RISC V is an open source specification of an Instruction Set Architecture (ISA).x86 is commonly used ISA for personal computers and ARM architecture is commonly used for Mobile Phones .But these are proprietary standards Unlike most other ISA designs, the RISC-V ISA is provided under open source licenses that do not require fees to use, which provides it a huge edge over other commercially available ISAs. It is a simple, stable, small standard base ISA with extensible ISA support, that has been redefining the flexibility, scalability, extensibility, and modularity of chip designs. 

## Assembly language - Bridge between software and hardware
Applications and softwares running in our laptops and PCs are written in a variety of languages like C, C++, Python and Java etc. These different languages cannot be directly processed and implemented by the hardware (Microprocessor Core). They need to be converted into form that is understandable by the core. This is where the ISA or the instruction set architecture (assembly language) comes into the picture. Special programs called the compiler and assembler are used to convert the instructions in different languages to the target assembly language. The compiler converts the code into assembly language, which in our case is the RISC-V ISA and the assembler takes care of converting the different instructions in textual format into binary representations which are understood by the microprocessor and executed.

## PicoRV32 and PicoSOC 
with the advent of an open standard ISA like RISC-V, there are many people who have created their own microprocessor cores which are documented in the RISC-V github handle found at [RISC-V Cores List](https://github.com/riscv/riscv-cores-list).PicoRV32 is one such implementation of the RISC-V ISA created by clifford wolf. [PicoRV32](https://github.com/cliffordwolf/picorv32) is a CPU core that implements the RISC-V RV32IMC Instruction Set. It can be configured as RV32E, RV32I, RV32IC, RV32IM, or RV32IMC core, and optionally contains a built-in interrupt controller. [PicoSOC](https://github.com/cliffordwolf/picorv32/blob/master/picosoc/picosoc.v) is an example SOC created using PicoRV32. PicoSOC is a type of SOC flavour which is more suited towards **FPGA** implementation and contains a less-detailed description of memory and other Components since these are resources already available inside the FPGA.
<p align="center">
<img src="http://opencircuitdesign.com/qflow/giffiles/raven_anno2.png" 
alt="alt text" width = 516 height = 452 >
<p/>

# Digital ASIC Design Flow - Components

## RTL IPs
Register-transfer level IPs refer to description of an Digital IP (Intellectual property) using Hardware Description Languages like Verilog, System Verilog and VHDL. This is one of the three requirements for an ASIC design flow. There are hundreds of RTL opensource designs available all over the internet. Some of the popular websites for opensource RTL IPs include [Opencores](https://opencores.org/), [LibreCores](https://www.librecores.org/) and [Github](https://github.com/). 

## EDA Tools
Electronic design automation (EDA), also referred to as electronic computer-aided design (ECAD), is a category of software tools for designing electronic systems such as integrated circuits and printed circuit boards. The final GDS files are generated from the RTL IPs using these tools. Various Academic projects have been made opensource, some of the popular ones include [OpenRoad](https://github.com/The-OpenROAD-Project),[Openlane](https://github.com/efabless/openlane) and [Qflow](https://github.com/RTimothyEdwards/qflow). 

## PDKs
Process Design Kit(PDKs) are a collection of files used to model a fabrication process for the EDA tools used to design an IC. The files describe the Process Design Rules(DRC, LVS etc), Device Models (SPICE), Digital Standard Cell Libraries, I/O Libraries etc. This is the third most important enabler for digital ASIC flow. PDKs formed the interface between Pure Play Fabs and Fabless design companies. Since the beginning of the semiconductor industry, PDKs were not openly available.
The [SkyWater Open Source PDK](https://github.com/google/skywater-pdk) targeting the SKY130 process node is a collaboration between Google and SkyWater Technology Foundry to provide a fully open source Process Design Kit and related resources, which can be used to create manufacturable designs at SkyWater’s facility. 

# Simplified RTL2GDS flow

1. Synthesis :- Converts the RTL description made using any of the HDLs into a circuit or a netlist of components from the standard cell library. Standard cells are building blocks which make up the library. They have a regular layout. Each have different views/models.
2. Floor and Power Planning :- Objective here is to plan the silicon area and create robust power distribution network for the circuits.Floorplanning can be of two types namely the entire chip floor planning and macro floor planning. Chip floor-planning refers to partitioning the chip die between different system building blocks and placement of the I/O pads. Macro Floor-planning refers to the Dimensions, pin locations and row definitions of a Macro. In power planning, power network are constructed. Typically, a chip is powered by multiple Vdd and gnd pins. The power pins are connected to the circuits by vertical and horizontal power straps. such parallel structures helps reduce resistance and electro-migration problem. Typically, they use the upper metal layers.
3. Placement :- Refers to the placement of the cells on the floorplan rows aligned with the sites.connected cells are expected to be placed nearby for easier routing in further stages. typical placement happend in two steps. Global and Detailed 
