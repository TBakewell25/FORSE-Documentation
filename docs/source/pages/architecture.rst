Architecture Overview
=====================

The Big Picture
---------------

.. Describe what FORSE models at a high level: individual trees on a spatial grid,
   gap dynamics (trees compete for light, die, regenerate), boreal forest context.
   Clarify scope — what the model does and intentionally does not do.
   Good place to mention the SIBBORK lineage (Brazhnik & Shugart 2015).


Module Map
----------

.. A brief table mapping each source file to its role. Fill in the Description column.
   Remove any rows for files that are not relevant to core functionality.

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - File
     - Description
   * - ``forse.py``
     -
   * - ``allomlib.py``
     -
   * - ``zeliglib.py``
     -
   * - ``light3d.py``
     -
   * - ``weather.py``
     -
   * - ``soil.py``
     -
   * - ``load_driver.py``
     -
   * - ``logging_treatment.py``
     -
   * - ``hdf_analysis.py``
     -
   * - ``utils.py``
     -
   * - ``cherry_tree.py``
     -


Key Concepts
------------

.. Define the vocabulary a new developer needs before reading the rest of the docs.
   These are terms used throughout the codebase that may not be obvious.

.. glossary::

   Gap Model
      .. Define what a gap model is in the context of forest ecology.

   Plot Grid
      .. Describe the nx × ny spatial grid and what a single "plot" represents.

   Driver File
      .. One-sentence definition — expand on this in driver_reference.rst.

   Allometric Equation
      .. What allometry means here: relating DBH to height, leaf area, biomass.

   Stress Factor
      .. The 0–1 multiplier system used to limit tree growth.

   Growing Degree Days (GDD)
      .. Brief definition and why it matters for boreal forest simulation.


External Dependencies
---------------------

.. Describe what each major library is specifically used for in FORSE.
   Do not just list them — explain the role each one plays.

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Library
     - Role in FORSE
   * - NumPy
     -
   * - Numba
     -
   * - h5py
     -
   * - GDAL / rasterio
     -
   * - xarray
     -
   * - Dask
     -
