Architecture Overview
=====================

The Big Picture
---------------

FORSE is a spatially-explicit individual based forest model. It draws on both historical data, measured observations, and simulated climate information 
to model how forest stands might change in the future. What is now FORSE originated in 1988 as the FORTRAN written ZELIG model, a forest gap model developed
for simulation of boreal ecosystems. After being converted to Python in the 2000s and becoming the SIBBORK model, it was extended to operate on different biomes,
with different data sets, and with new features in what is now the **FOR**\est **S**\patially **E**\xplicit model.

This documentation aims to provide a description of its implementation details that will allow future developers to maintain it so that it can continue to grow 
and provide insight in support of earth sciences.

Module Map
----------

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - File
     - Description
   * - ``forse.py``
     - The core of the model. The ``main()`` function marking the entry point is here.
   * - ``allomlib.py``
     - A file containing various available allometric functions. (Deprecated?)
   * - ``zeliglib.py``
     - A file of allometric functions and data from the Zelig model. (Deprecated)
   * - ``light3d.py``
     - 3D light tracing routines for sunlight.
   * - ``weather.py``
     - Routines for calculating weather effects (rainfall, growing degree days, etc.)
   * - ``soil.py``
     - Routines for permafrost and thaw calculations.
   * - ``load_driver.py``
     - Simple wrapper functions for reading the driver in. (Deprecated)
   * - ``logging_treatment.py``
     -  Routines for simulating logging activity and effects.
   * - ``hdf_analysis.py``
     - A script for analyzing the resulting HDF5 output files from the model.
   * - ``utils.py``
     - Utility routines that aid in fetching and parsing data, called from ``forse.py``.


Key Concepts
------------

.. glossary::

   Gap Model
      A "gap model" is a dynamic forest model that simulates how changes in canopies affect forest growth.
      FORSE is "individual based", which is to say it does this simulation tree-by-tree.

   Spatially Explicit
      FORSE is unique in that it is spatially explicit. It uses precise, location specific data for its fine-grained simulation.

   Plot Grid
      In FORSE forest stands are represented as a rectangular grid of plots. Plots interact with eachother across time-steps.

   Driver File
      A Python file fed to the model that uses configuration settings and input data to build a dictionary that the runtime draws from.

   Allometric Equation
        An equation that describes how various features of an organism change relative to its size as it grows. In FORSE these can 
        be specified on a species-by-species basis in the driver file.

   Stress Factor
        A 0 to 1 multiplier that is used to limit the growth of trees which have been stressed. Causes of tree stress could be lack of rainfall or
        poor temperature conditions among other things.

   Growing Degree Days (GDD)
      The cumulative units of temperature above some baseline that a species experiences over some period. In order for a species to grow and thrive 
      it needs to achieve some number of GDDs.


External Dependencies
---------------------

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Library
     - Role in FORSE
   * - NumPy
     - A common and powerful data science library for Python. It's vectorized implementation allows fast manipulation of tensors.
   * - Numba
     - A library that allows for Python code to be compiled for better performance. Used in FORSE for intensive ray-tracing code.
   * - h5py
     - A library that helps with creating and writing HDF5 files in Python.
   * - GDAL / rasterio
     - An open source earth sciences data library that allows for reading DEMs  (among other available formats).
   * - xarray
     - 
   * - Dask
     -
