**VLSI 1: HDL based design for FPGAs - Notes week 2**

- Author: Ruben Schenk
- Date: 29.12.2021
- Contact: ruben.schenk@inf.ethz.ch

### 1.2.3 The fabrication point of view

**Field-programmable logic (FPL)** uses neither custom layout structures nor proprietary photomasks. Instead, pre-manufactured subcircuits get configured into the target via purely electrical means, such as programmable links.

Compared to mask-programmed ICs:

- Easy and extremely fast to modify
- I/O subcircuits, clock and power distribution, embedded memories, testability, etc. come at no extra effort shut in the component
- Large overhead in terms of area, delay and energy

The basic structure of an FPGA is composed of the following elements:

- Look-up table (LUT) in Logic Block: Performs the logic operations
- Flip-Flop (FF): Performs the storing of the results of the LUT
- Wires: Connect elements to one another, both Logic and clock
- Input/Output pads: These physically available ports get signals in and out of the FPGA

## 1.3 Business

## 1.4 Front-end

## 1.5 Back-end

## 1.6 CMOS Compared To Other Logic Families