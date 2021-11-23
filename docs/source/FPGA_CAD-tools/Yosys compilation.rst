.. _yosys:

Yosys compilation
=================

Yosys is used for logic synthesis and technology mapping of the Verilog Hardware Description Language (HDL) into a JSON(Nextpnr) or BLIF(VPR) netlist.

Building
--------

To build you may use the Makefile wrapper in the clone repository (https://github.com/YosysHQ/yosys.git) ``make`` and ``sudo make install``

User guide
----------

A pre-defined Yosys TCL script is under ``$FAB_ROOT/nextpnr/fabulous_v3/synth/synth_fabulous_dffesr.tcl`` for fabuloud version3, 

.. code-block:: console

	yosys -p "tcl <path/to/TCL/File>/synth_fabulous_dffesr.tcl <K-LUT> <benchmark_top_module> <output>.json" <benchmark_netlist>.v

* For any clocked benchmark, a clock tile blackbox module must be instantiated in the top module for clock generation.

.. code-block:: verilog 

        wire clk;
        (* keep *) Global_Clock inst_clk (.CLK(clk));

* Yosys models files, all can be found under ``$FAB_ROOT/nextpnr/fabulous_v3/synth``

+---------------+-----------------------------------------------------------------------+
| File Name     | Description                                                           |
+===============+=======================================================================+
| prims_ff.v    | Fabric primitives definition                                          |
+---------------+-----------------------------------------------------------------------+
| ff_map.v      | Abitrary D-type Flip-flop with SET/RESET and ENABLE technology mapping|
+---------------+-----------------------------------------------------------------------+
| latches_map.v | Latches technology mapping                                            |
+---------------+-----------------------------------------------------------------------+
| cells_map_ff.v| LUT-4 technology mapping (can be modified to LUT-6)                   |
+---------------+-----------------------------------------------------------------------+

Example
-------

The following are simple command-line to synthesis the netlist ``sequential_16bit`` into JSON netlist.

.. code-block:: console

	yosys -p "tcl ../synth/synth_fabulous_dffesr.tcl 4 sequential_16bit sequential_16bit.json" sequential_16bit.v




