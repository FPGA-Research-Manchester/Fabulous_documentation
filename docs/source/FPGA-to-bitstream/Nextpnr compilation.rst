Nextpnr compilation
===================

Compile JSON to FASM by nextpnr <-- bels.txt + pips.txt

Nextpnr-fabulous is using nextpnr python API for architecture generation, then pack, place and route.

Building
--------

.. code-block:: console

   cd $FAB_ROOT/nextpnr
   cmake . -DARCH=fabulous_v3
   make -j$(nproc)
   sudo make install

.. note:: Any new version architecture should be declared in ``$FAB_ROOT/nextpnr/CMakeLists.txt``

User guide
----------

To generate the FASM file by nextpnr, go into ``$FAB_ROOT/nextpnr/fabulous_v3/fab_arch``,

.. code-block:: console

        nextpnr-fabulous --pre-pack fab_arch.py --pre-place fab_timing.py --json <JSON_file> --router router2 --post-route bitstream.py

+------------------+------------------------------------------------+
| <JSON_file>      | the JSON file generated from Yosys compilation |
+------------------+------------------------------------------------+

Example,

.. code-block:: console
        
        nextpnr-fabulous --pre-pack fab_arch.py --pre-place fab_timing.py --json 16bit-sequential.json --router router2 --post-route bitstream.py

Constraints for the placement of IO/bels
----------------------------------------

Constraints for your architecture can be put in place using Absolute Placement Constraints ``(* BEL="X2/Y5/lc0" *)``. For example,

.. code-block:: verilog

        (* BEL="X7Y3.C" *) FABULOUS_LC #(.INIT(16'b1010101010101010), .DFF_ENABLE(1'b0)) constraint_test (.CLK(clk), .I0(enable), .O (enable_i));

We can constrain which BEL to be used in the routing resource, LUT "C" is constrained to be used in Tile X7Y3 as shown in the example. With the same constrain method, we can also declare ``InPass4_frame_config, OutPass4_frame_config and IO_1_bidirectional_frame_config_pass`` for IO constrains.       


Primitive instantiation
-----------------------

As described in more detail in the yosys documentation, the (*keep*) attribute can be used to instantiate a component and clarify that yosys should not try to optimise it away. This is done in the format

.. code-block:: none

        (* keep *) COMPONENT_TYPE #(PARAMETER = VALUE)  COMPONENT_NAME(.PORT_NAME1(WIRE_NAME1), .PORT_NAME2(WIRE_NAME2), ...);



