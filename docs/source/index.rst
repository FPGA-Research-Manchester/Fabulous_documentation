FABulous: an Embedded FPGA Framework
===================================

## Introdution
FABulous is designed to fulfill the objectives of ease of use, maximum portability to different process nodes, good control for customization, and delivering good area, power, and performance characteristics of the generated FPGA fabrics. The framework provides templates for logic, arithmetic, memory, and I/O blocks that can be easily stitched together, whilst enabling users to add their own fully customized blocks and primitives.

The FABulous ecosystem generates the embedded FPGA fabric for chip fabrication, integrates 
[SymbiFlow](https://symbiflow.github.io/) 
toolchain release packages, deals with the bitstream generation and after fabrication tests. Additionally, we will provide an emulation path for system development.

This guide describes everything you need to set up your system to develop for FABulous ecosystem.

Ways to run Symbiflow on these devices will be explained in the near future.

<img src="https://www.dropbox.com/s/g6wrtom681nr7tb/fabulous_ecosystem.png?raw=1" width="600"/>

Check out the :doc:`usage` section for further information, including
how to :ref:`installation` the project.

.. note::

   This project is under active development.

Contents
--------

.. toctree::

   usage
   1) Create fabric RTL
      1.1) Generate ASIC constraints
      1.2) Generate compile scripts?
      1.3) Run Kelvin optimizations
      1.4) Generate nextpnr timing model
      1.5) Generate VPR timing model
      1.6) Generate Meta Data (bitstream bit masking)
   2) Create Yosys models
   3) Create nextpnr models
      3.2) Create nextpnr primitives (bels.txt)
      3.1) Create nextpnr architecture graph (pips.txt) <-- timing model
   4) Create VPR models 
      4.1) Create VPR primitives (architecture.xml)
      4.2) Create VPR architecture graph (routing_resources.xml) <-- timing model
   5) Compile VHDL/Verilog to JSON/BLIF by Yosys <-- Yosys models
   6) Compile JSON to FASM by nextpnr <-- bels.txt + pips.txt
   7) Compile BLIF to FASM by VPR <-- architecture.xml + routing_resources.xml
   8) Generate Bitstream <-- Meta Data
   9) Generate simulation model/ emulation model (Verilog or VHDL)
