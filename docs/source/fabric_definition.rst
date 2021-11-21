Fabric definition 
=================

This section describes the process of modeling FABulous fabrics in a top-down manner.
FABulous can reuse preimplemented tiles which allows it to define fabrics in a LEGO-like manner where it is sufficient to define the :ref:`fabric_layout` in terms of IO and logic tiles or any other required block.

For customization of :ref:`tiles` or the creation of new blocks, it is possible to model the routing :ref:`wires`, a central :ref:`switch_matrix` and :ref:`primitives`

The following figure shows a small fabric, which we will model throughout this section. It provides: 

* 4x IO pins
* 4x Register file slices
* 2 DSP block supertiles 
* 4 internal IO ports for coupling the fabric with a CPU

.. figure:: figs/abstract_tile_view.*
    :alt: FABulous example fabric
    :width: 33% 
    :align: center
    
The full model of a fabric is described by the following files:

* A file :ref:`fabric_csv` providing the :ref:`fabric_layout`, some global settings, and the descriptions of the :ref:`tiles`-
* A set of list files (\*.list) desribing the adjaceny list of the switch matrix for each of the used tiles or the corresponding adjaceny matrix as a csv file
* A set of optional bitstream mapping csv files
* A set of primitives used

The following block provides a fabric.csv example. 

.. code-block:: python
   :emphasize-lines: 1,6,8,15,17,24

   FabricBegin      # explained in subsection Fabric layout
   NULL,  N_term,   N_term,   N_term,  N_term,  NULL
   W_IO,  RegFile,  DSP_top,  LUT4AB,  LUT4AB,  CPU_IO
   W_IO,  RegFile,  DSP_bot,  LUT4AB,  LUT4AB,  CPU_IO
   ...
   FabricEnd        

   ParametersBegin      
   ConfigBitMode, frame_based        # default is FlipFlopChain
   FrameBitsPerRow, 32               # configuration bits per tile row 
   MaxFramesPerCol, 20               # configuration bits per tile column
   Package, use work.my_package.all; # populate package fiels in VHDL code generation
   GenerateDelayInSwitchMatrix, 80   # we can annotate some delay to multiplexers
   MultiplexerStyle, custom          # 
   ParametersEnd        
   
   TILE, LUT4AB      # explained in subsection Tiles            
   #direction  source_name  X-offset  Y-offset  destination_name  wires
   NORTH,      N1BEG,       0,        1,        N1END,            4
   ...
   BEL,      LUT4c_frame_config_OQ.vhdl,  LA_
   ...
   MATRIX,   LUT4AB_switch_matrix.vhdl               
   EndTILE                  

.. _fabric_csv:

Fabric csv description
----------------------

* For the part between ``FabricBegin`` and ``FabricEnd``, refer to the :ref:`fabric_layout` description.

* Empty lines will be ignored as well as everything that follows a ``#`` \(the **comment** symbol in all FABulous descriptions\).

* Parameters that relate to the fabric specification are  encapsulated between the key words ``ParametersBegin`` and ``ParametersEnd``.

  Parameters that relate to the flow are passed as comand line arguments.
  
  Parameters have the format <key>,<value>
  
  FABulous defines the following parameters:
  
  * ``ConfigBitMode``, ``[frame_based|FlipFlopChain]``

    FABulous can write to the configuration bits in a frame-based organisation very similar to what most industry FPGAs do. This supports partial reconfiguration and is (except for tiny fabrics) superior in any sense (configuration speed, resource cost, power consumption) over flip flop scan chain configuration (which is was most other open source FPGA frameworks opted for). 
    
    Configuration readback is not supported so far as it was considdered inaffective for embeddd FPGA use cases.

  * ``FrameBitsPerRow``, ``unsigned_int``
    
    In frame-based configuration mode, FABulous will build a configuration frame register over the height of the fabric and providing the specified number of data bits per row. This whill generate frame_data wires in the fabric, which correspond to bitlines in a memory organisation. 
      
    Note that the specified size corresponds to the width of the parallel configuraton port and 32 bits is the most sensible configuration for most systems.

    Currently, we set ``FrameBitsPerRow`` globally for all rows but we plan to extend this to allow for resurce type specific adjustments in future versions. 
    For instance, the tiles at the north border of a fabric may only provide some fixed U-turn routing without the need of any configuration bits and we could reflect this by removing all frame_data wires in the top row. This extension may include an automatic adjustment mode.
      
  * ``MaxFramesPerCol``, ``unsigned_int``
    
    For the frame-based configuration mode, this will specify the number of configurations frames a tile may use. The total number of configuration bits usable is:
      
      ``FrameBitsPerRow`` x ``MaxFramesPerCol``
      
    Note that we can leave possible configuration bits unused and that no configuration latches will be generated for unused bits.
      
    FABuloud whill generate the specified number of vertical frame_strobe wires in the fabric, which correspond to wordlines in a memory organisation. 
      
    ``FrameBitsPerRow`` and ``MaxFramesPerCol`` should be around the same number to minimize the wiring resources for driving the configuratoin bits into the fabric. In most cases, only ``MaxFramesPerCol`` will be adjusted to a number that can accomodate the number of configuration bits needed.
      
    Currently, we set ``MaxFramesPerCol`` globally for all resource types (e.g., LUTs and DSP block columns) but we plan to extend this to allow for resurce type specific adjustments.
    This feature may include an automatic adjustment mode.      

  * ``Package``, ``string``
    
    This option will populate the package declaratio block on VHDL output mode with the string to declare a package.  

  * ``GenerateDelayInSwitchMatrix``, ``unsigned_int``
    
    This option will annotate the specified time in ps to all switch matrix multiplexers. This ignored for synthesis but allows simulating the fabric in the case of configured loops (e.g., ring-oscillators). 

  * ``MultiplexerStyle``, ``[cutom|TODO]``

    FABulous can generate the switch matrix multiplexers in different styles including behavioral RTL, instantiating standard cell primitives and instantiation of full custom multiplexers.

    The latter is implemented by replacing a defined n-input multiplexer with a predefined template. For instance, for the Skywater 130 process, we provide a transmission gate-based custom MUX4. In the case of requiring a MUX16, FABulous will synthesize this multiplexer to use 4 + 1 of our custom cells.

    .. note::  So far, FABulous fabrics are using fully (binary) encoded multiplexers (e.g., a MUX16 requires 4 configuration bits). However, the major vendors Xilinx and Intel use highly optimized SRAM cells where a configuration cell may directly controll a passtransistor (e.g., this is used in Xilinx UltraScale fabrics). For a MUX16, this requires 2 x 4 = 8 configuration bits, but is slightly better in area as omits a decoder.
	We plan to extend the FABulous switch matrix compiler accordingly.

.. _fabric_layout:

Fabric layout
-------------

FABulous models FPGA fabrics as simple CSV files that describe the fabric layout in terms of :ref:`tiles`.
A tile is the smallest unit in a fabric and typically hosts primitives like a CLB with LUTs or an I/O block.
Multiple smaller tiles can be combined into :ref:`supertiles`.
The following figure shows the fabric.csv representation of our example fabric as shown in a spreadsheet program.

.. figure:: figs/Fabric_spreadsheet.*
    :alt: FABulous example fabric in csv representation
    :width: 60% 
    :align: center

.. code-block:: python
   :emphasize-lines: 1,6

   FabricBegin      
   NULL,  N_term,   N_term,   N_term,  N_term,  NULL
   W_IO,  RegFile,  DSP_top,  LUT4AB,  LUT4AB,  CPU_IO
   W_IO,  RegFile,  DSP_bot,  LUT4AB,  LUT4AB,  CPU_IO
   ...
   FabricEnd        

* The fabric layout is encapsulated between the key words ``FabricBegin`` and ``FabricEnd``.

  The specified tiles are references to tile descriptors (see :ref:`tiles`).
  The tiles form a coordinate system with the origin in the top-left:

  +-------+-------+-------+------+
  | X0Y0  | X1Y0  | X2Y0  | ...  |
  +-------+-------+-------+------+
  | X0Y1  | X1Y1  | X2Y1  | ...  |
  +-------+-------+-------+------+
  | ...   | ...   | ...   | ...  |
  +-------+-------+-------+------+

  ``NULL`` tiles are used for padding and no code will be generated for these. ``NULL`` tiles can be used to build non-rectangular shaped fabrics.

.. _tiles:

Tiles
-------------

.. figure:: figs/tile_CLB_example.*
    :alt: Basic tile illustration
    :width: 30% 
    :align: center
	
A tile is the smallest unit in a fabric and a tile provides
 
* A sescription of :ref:`wires` to adjacent tiles

* A central :ref:`switch_matrix`

* An optional list of :ref:`primitives`

* A central configuration storage module 

A tile typically hosts primitives like a CLB with LUTs or an I/O block.
Multiple smaller tiles can be combined into :ref:`supertiles` to accomodate complex blocks like DSPs.
Each tile that is refered to in the :ref:`fabric_layout` requrires to specify the corresponding tile description in the fabric.csv file that has the following format:  

.. code-block:: python
   :emphasize-lines: 1,12

   TILE, LUT4AB      # define tile name            
   #direction  source_name  X-offset  Y-offset  destination_name  wires
   NORTH,      N1BEG,       0,        1,        N1END,            4
   EAST,       E2BEG,       1,        0,        N2END,            6
   JUMP,       J_BEG,       0,        0,        J_END,            12
   ...
   #         RTL code                     optional prefix
   BEL,      LUT4c_frame_config_OQ.vhdl,  LA_
   BEL,      LUT4c_frame_config_OQ.vhdl,  LB_
   ...
   MATRIX,   LUT4AB_switch_matrix.vhdl               
   EndTILE  

.. _wires:

Wires
~~~~~

Wires are defined as 5-tuples:

``direction``,  ``source_name``,  ``X-offset``,  ``Y-offset``,  ``destination_name``,  ``wires``

specifying:

* ``direction``, ``[NORTH|EAST|SOUTH|WEST|JUMP]``

  The keyword ``JUMP`` specifies a stop-over at the switch matrix, which is a logical wire that starts and ends at the same switch matrix (i.e. ``X-offset`` = 0 and ``Y-offset`` = 0).
  
  Jump wires are useful to model hierarchies, some sharing of multiplexers or tapping into routing paths, as shown in the examples below. 
  
  In the VPR and Altera world, tiles separate between a connection switch matrix and the actual local wire switch matrix. The connection switch matrix is nothing else as a bank of multiplexers selecting from the local routing wires a pool of connection wires that can then be further routed to primitive pins (e.g, a LUT input). In FABulous, those connection wires would be modelled with a set of jump wires, which connect somehow the primitive input multiplexers.
  
  Older Xilinx architectures have a less hierarchical routing graph and local routing wires between the tiles connect drectly to the input multiplexers of the primitives. 
  
  Xilinx Virtex-5 FPGAs provide diagonal routing wires (e.g., a Wire routing in north-east direction), a concept abbandoned in consecutive Xilinx FPGA families. FABulous can model diagonal routing by splitting a wire in its components (e.g., a north-east wire can be modeled by cascading a north wire and an east wire).
  
* ``source_name``, ``string``

  ``destination_name``, ``string``

  These are symbolic names for the ports used for the tile top wrapper and the switch matrix connections.
  It is recomended to follow a semantic that expresses the direction, routing span (i.e. how many tiles far away) if it is a *begin* or *end* or other port.
  For instance, a single wire in NORTH direction should use names like *N1Beg* to *N1End* or *N1b* to *N1e*
  
  .. note::  The ``destination_name`` is refering to the port name used at the destination tile.
  
* ``X-offset``, ``signed_int``

  ``Y-offset``, ``signed_int`` 
  
  .. figure:: figs/wire_tile_grid.*
    :alt: Basic tile illustration
    :width: 40% 
    :align: center

  FABulous models wires strictly in horizontal or vertical direction but never directly in diagonal direction, as this reflects directly the tiled physical implementation of the fabric.
  Therefore, in each wire specification, either ``X-offset`` is ``0`` or ``Y-offset`` is ``X-offset`` or both are ``0`` (in the case of a JUMP wire.
  
* ``wires``, ``unsigned_int``
  Specifies the number of wires.
  
  FABulous will index the wires of each entry starting from [0].

A metric that is important for FPGA ASIC implementations is the channel *cut* number, which is denoting the number of wires that must be accomodated between two adjacent tiles. The cut number is an indicator for the congestion to be expected when stitching together the fabric. Let us take the following example: 

.. code-block:: python
   :emphasize-lines: 1

   TILE, Example_tile      # define tile name            
   #direction  source_name  X-offset  Y-offset  destination_name  wires
   EAST,       E1Beg,       1,        0,        E1End,            6
   WEST,       W4Beg,       -4,       0,        W4End,            3
   
The following figure shows the corresponding wiring between the tiles.
Note that a wire with a span greater 1 is usually nested.

.. figure:: figs/wires_model.*
  :alt: Basic tile illustration
  :width: 40% 
  :align: center

Each entry in the wire specification contributes with max(abs(``X-offset``),abs(``y-offset``)) x ``wires`` to the cut number.
In this example, the east single wire (E1Beg) is contributing with 1 x 6 = 6 and the west quad wire (W4Beg) with 4 x 3 = 12wire segments to he cut. 
Therefore, even we have only half the number of quad wires, these contribute double the single wires to the cut.
Furthermore, the wires needed to write the configuration into the configuration memory cells are conributing substantially to the cut (see parameter ``FrameBitsPerRow`` in section :ref:`fabric_csv`).

.. note::  A typical CLB requires about 100 to 200 wire connections between adjacent tiles. 

 The shift register configuration mode needs less wire connections than frame-based configuration but shift register mode intends to have a slightly higher congestion inside the tiles because of the long chain.

.. note::  Because long distance wires contribute overproportionally to the cut number it can be beneficial to segment long ditance wires to better balance the silicon core area to the available metal stack for implementing the routing.

.. _switch_matrix:

Switch matrix
~~~~~~~~~~~~~~~

FABulous usually implements all routing in a central switch matrix. 
The inputs to the switch matrix are the wire end ports of the local wires and JUMP wires and the outputs of the :ref:`primitives`.
Hierarchies in an FPGA architecture graph will usually be modelled through JUMP wires (as shown in the :ref:`tiles` figure).
However, while it is possible to have multiple switch matrices in a tile, this is not recommended.

Configurable connections are defined in either an adjacency list or an adjacency matrix.

**Adjacency list** files follow the naming convention <tile_descriptor>_switch_matrix.list (e.g., LUT4AB_switch_matrix.list).

A switch matrix entry is specified by a line <output_port>,<input_port>.
For convinience, it is possible to specify multiple ports though a list operator [item1|item2|...].
For instance, the following line in a list file

.. code-block:: python

   [N|E|S|W]2BEG[0|1|2],[N|E|S|W]2END[0|1|2] # extend double wires in each direction
   
is equivalent to

.. code-block:: python

   N2BEG0,N2END0 # extend double wires in each direction
   E2BEG0,E2END0 # extend double wires in each direction
   S2BEG0,S2END0 # extend double wires in each direction
   W2BEG0,W2END0 # extend double wires in each direction
   N2BEG1,N2END1 # extend double wires in each direction
   E2BEG1,E2END1 # extend double wires in each direction
   S2BEG1,S2END1 # extend double wires in each direction
   W2BEG1,W2END1 # extend double wires in each direction
   N2BEG2,N2END2 # extend double wires in each direction
   E2BEG2,E2END2 # extend double wires in each direction
   S2BEG2,S2END2 # extend double wires in each direction
   W2BEG2,W2END2 # extend double wires in each direction
   
The example shows how port names can be composed from string segments that can alternatively be provided in list form. The lists will be recursevely unwrapped, which allows it to use multiple list operators together.

An error message is generated if the number of composed ort names differs for the number of input_ports and output_ports or if ports are not found.
A warning will be generated if am already specified connection is tried to be set again.

A switch matrix multiplexer is modeled by having multiple connections for the same <output_port>. For example, a MUX4 can be modeled as:

.. code-block:: python

   N2BEG0,N2END3 # cascade and twist wire index
   N2BEG0,E2END2 # turn from east to north
   N2BEG0,S2END1 # U-turn
   N2BEG0,LB_O   # route LUT B output north

   # the same in compact form:
   N2BEG[0|0|0|0],[N2END3|E2END2|S2END1|LB_O]

Adjacency lists are better for specifying and mantaing the connections while an adjaceny matrix is better for monitoring and debug. 
FABulous works on adjacency matrices and the tool can translate arbitrarily between both.
**Adjacency matrix** files are csv files and follow the naming convention <tile_descriptor>_switch_matrix.csv (e.g., LUT4AB_switch_matrix.csv).
The following figure shows a list file and the corresponding adjacency matrix:

.. figure:: figs/adjacency.*
  :alt: Basic tile illustration
  :width: 90% 
  :align: center
  
The adjaceny matrix states the tile identifyer name in the top left cell.
The columns denote the input ports to the switch matrix and the rows the output ports.
A ``1`` in the matrix demotes a configurable connection (i.e. a multiplexer input connection) and each ``1`` corresponds to a <output_port>,<input_port> tuple defined in the adjacency list.
Therefore, each row is corresponding to one switch matrix multiplexer.

When generating the adjacency matrix, FABulous will annotate for each row and column the number of connections set.
For the rows, this denotes the size of the multiplexers (e.g., MUX4) and by checking the column summery, we can insspect how well the wire usage is balanced.

.. note::  Note that we can define the port names ``VCC`` and ``GND`` in :ref:`wires`, which allows it to specify a configurable multiplexer setting to ``1`` or ``0``. For instance, this is useful for BRAM pins where unused ports (e.g., some MSB address bits) have to be tied to ``0`` without the need of any further LUTs or routing.

.. note::  The multiplexers in the switch matrices are controled by configuration bits only. 

 The multiplexers in :ref:`primitives` can either be controlled by configuration bits (e.g., to select if a LUT output is to be routed to a primitive output pin or through a flop) or by the user logic (e.g., to cascade adjacent LUTs for implementing larger LUTs (like the F7MUX and F8MUX multiplexers in Xilinx FPGAs with LUT6).

.. note::  Defining the adjaceny of a switch matrix (and the wires) is a difficult task. Too many connections and wires are expensive to implement and will result in poor density and potentially in poor performance. However, to few connections and wires may not allow to implement the intended user circuits on the fabric in the first place. The latter issue is not easily solvable by leaving primitives unused because that requires, for example, to use more CLBs. That, in turn, requires more wires between the tiles, and will therefore jeopardizing the approach of underutilzing the CLBs.

 Another difficulty is setting good switch matrix connections. An architecture graph should have sufficient entropy because of the usual sparsity of the graph. For instance, if we have to route from a LUT to a specific DSP pin, and the first path is not hitting that pin, then using an alternative path should result in possible connections to a different subset of pins. This implies that the architecture graph should not state linear combinations in subgraphs. However, adjacent LUT inputs often share the same signal (e.g., when dascading two LUT6 to form one LUT7, the two LUT6 connect to the same 6 signals). This can be used to share some multiplexing in the switch matrices.

 To simplify the definition of fabrics, the provided FABulous reference fabrics had been confirmed to implement non-trivial user circuits like different CPU cores.
 The provided switch matrices can be easily reused in new custom tiles (it is standard to have mostly identical switch matrices throughout an FPGA fabric, even if resources (LUTs, BRAMs, DSPs) differ).
 Moreover, downstripping the routing fabric is easily possible by removing wires and connections.

.. _primitives:

Primitives
~~~~~~~~~~


.. _supertiles:

Supertiles
----------

and the corresponding spreadsheet specification from which FABulous can generate an eFPGA fabric.



  -tile
  -primitives
  -routing fabric
  -resource columns
  -interfaces
  -shape

