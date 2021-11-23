FABulous: an Embedded FPGA Framework
====================================

FABulous is designed to fulfill the objectives of ease of use, maximum portability to different process nodes, good control for customization, and delivering good area, power, and performance characteristics of the generated FPGA fabrics. The framework provides templates for logic, arithmetic, memory, and I/O blocks that can be easily stitched together, whilst enabling users to add their own fully customized blocks and primitives.

The FABulous ecosystem generates the embedded FPGA fabric for chip fabrication, integrates
`SymbiFlow <https://symbiflow.github.io/>`_
toolchain release packages, deals with the bitstream generation and after fabrication tests. Additionally, we will provide an emulation path for system development.

This guide describes everything you need to set up your system to develop for FABulous ecosystem.

.. figure:: figs/workflows.svg
    :alt: Fabulous workflows and dependencies
    :width: 80%
    :align: center
    
    Fabulous workflows and dependencies.

.. image:: https://www.dropbox.com/s/g6wrtom681nr7tb/fabulous_ecosystem.png?raw=1
    :width: 80%

Check out the :doc:`Usage` section for further information, including
how to :ref:`setup`.

.. note::

   This project is under active development.

Contents
--------

.. toctree::
   :maxdepth: 1 
   :caption: Background and Features

   background

.. toctree::
   :maxdepth: 2

   Usage


.. toctree::
   :maxdepth: 1 
   :caption: Fabric definition

   fabric_definition

.. toctree::
   :maxdepth: 2 
   :caption: Fabric ASIC implementation

   ASIC/index

.. toctree::
   :maxdepth: 2 
   :caption: FPGA CAD-tool parameterization

   FPGA_CAD-tools/index

.. toctree::
   :maxdepth: 2 
   :caption: FPGA-to-bitstream compilation

   FPGA-to-bitstream/index

..		Nextpnr path
.. 		VPR path
..		Bitstream assembly

.. toctree::
   :maxdepth: 1 
   :caption: Simulation and emulation

   simulation/index

..   Model validation
..   Test and characterization
   
.. toctree::
   :maxdepth: 2 
   :caption: Technical references

   references/index

.. toctree::
   :maxdepth: 1 
   :caption: Chip gallery

   gallery/index
   
.. toctree::
   :maxdepth: 1 
   :caption: Definitions and abbreviations

   definitions
   
.. toctree::
   :maxdepth: 1 
   :caption: Team and contact
   
   contact
   publications

