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

- ``Yosys/``
- ``nextpnr-fabulous/``

.. _installation:

Building Fabric
---------------

.. code-block:: console

   cd fabric_generator
   ./create_basic_files.sh
   ./run_fab_flow.sh


Generating Bitstream
--------------------

