Running the Model
=================

Once a virtual runtime environment has been created, and all dependencies have been installed, the FORSE model is ready to run.
FORSE requires two inputs to be provided explicitly: a driver file and a DEM. Additionally, an output HDF5 file must be specified.

Model Inputs
------------

The Driver File
^^^^^^^^^^^^^^^

FORSE makes use of a Python driver file to provide input data, run configuration settings, and modular allometric equations. Driver files
must be carefully created to match the specified interface. Individual driver files are associated with specific run sites, and much match 
the DEM file that is specified for the run. The driver file must be specified explicitly as an argument at runtime.

The DEM
^^^^^^^

A **D**\igital **E**\levation **M**\odel (DEM) is a common file formate for storing 3D elevation rasters for GIS applications. DEM files can be
identified by the extension ``.tif`` or ``.dem``. The FORSE model makes use of DEM data to enable precise simulation of radiation at run sites, 
and underpins the model's spatially-explicit aspects. The DEM must be specified explicitly as an argument at runtime.

Model Outputs
-------------

HDF5 Data
^^^^^^^^^

The primary output of FORSE is a single HDF5 file, the name of which is provided as an argument. The HDF5 is a hierarchical file that stores 
detailed output from the run. For each run the file stores yearly per-plot, per-species tables of the following:

    - DBH
    - Species Code
    - Basal Area
    - Biomass
    - Stem Density

In addition to these tables, the driver file used for the run is included, improving the reproducibility of this data.

Calling FORSE
-------------

The model can be called from the command line using the Python interpreter directly. The following are accepted as arguments:

    - ``-h``: print a help menu (not a required argument)
    - ``--no-numba``: run the model without doing Numba compilation (not a required argument)
    - ``-v``: run in verbose mode, print output to STDOUT (not a required argument)
    - ``-d``: print debug info to STDOUT (not a required argument)
    - ``--dem <input_dem>``: specify the input DEM (**required**)
    - ``--version``: print the model version (not a required argument)
    - ``--clean``: remove all project files except for DEM and driver after run (not a required argument)
    - ``<driver_file>``: the input driver file (**required**)
    - ``<output_file>``: output filename for hdf5 (**required**)

.. code-block:: console

    source/forse.py [-h] [--no_numba] [-v] [-d] [--dem DEM] <input_dem> [--version] [--clean] <driver_file> <output_file>

