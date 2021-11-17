Usage
=====

Prerequisites
-------------

The following packages need to be installed for generating fabric HDLs

- ``Python 3.5 or later/``

Install python dependancies

.. code-block:: console

   pip3 install -r requirements.txt

The following packages need to be installed for CAD toolchain

- ``Yosys``
  - latest version on Yosys git repository: https://github.com/YosysHQ/yosys.git
- ``nextpnr-fabulous``

.. code-block:: console

   git clone --branch fabulous https://github.com/FPGA-Research-Manchester/nextpnr
   cd nextpnr
   cmake . -DARCH=fabulous
   make -j$(nproc)
   sudo make install

.. _installation:

Building Fabric
---------------

.. code-block:: console

   cd fabric_generator
   ./create_basic_files.sh
   ./run_fab_flow.sh


Generating Bitstream
--------------------

.. code-block:: console

   cd ../nextpnr/fabulous/fab_arch/
   ./fabulous_flow.sh sequential_16bit
   python3 bit_gen.py -genBitstream sequential_16bit.fasm meta_data.txt sequential_16bit_output.bin

