Fabric definition 
=================

This section describes the process of modeling FABulous fabrics in a top-down manner.
FABulous can reuse preimplemented tiles which allows it to define fabrics in a LEGO-like manner where it is sufficient to define the :ref:`fabric_layout` in terms of IO and logic tiles or any other required block.

For customization of :ref:`tiles` or the creation of new blocks, it is possible to model the 

The following figure shows a small fabric, which we will model throughout this section. It provides: 

* 4x IO pins
* 4x Register file slices
* 2 DSP block supertiles 
* 4 internal IO ports for coupling the fabric with a CPU

.. figure:: figs/abstraqct_tile_view.*
    :alt: FABulous example fabric
    :width: 33% 
    :align: center
	

.. _fabric_layout:

Fabric layout
-------------

FABulous models FPGA fabrics as simple CSV files that describe the dabric layout in terms of tiles.
A tile is the smallest unit in a fabric and typically hosts primitives like a CLB with LUTs or an I/O block.
Multiple smaller tiles can be combined into a super tile to accomodate coplex blocks like DSPs.


.. _tiles:

Tiles
-------------

Wires
~~~~~

.. _supertiles:

Supertiles
-------------

and the corresponding spreadsheet specification from which FABulous can generate an eFPGA fabric.



  -tile
  -primitives
  -routing fabric
  -resource columns
  -interfaces
  -shape

