FABulous: an Embedded FPGA Framework
====================================

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
   api
   Fabric RTL
   Fabric models generation
   Yosys compilation
   Nextpnr compilation
   VPR compilation
   Bitstream generation
   Simulation model
