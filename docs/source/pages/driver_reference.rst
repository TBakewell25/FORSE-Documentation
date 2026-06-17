Driver File Reference
=====================

What a Driver File Is
---------------------

.. Explain the driver file concept:
   - A plain Python file that returns a configuration dictionary
   - Associates a specific run site with its parameters (DEM, climate, species)
   - Contains both static configuration and functions (e.g. return_warming_weather)
   - Why Python was chosen over JSON/YAML (flexibility for climate scenario functions)
   Mention load_driver.py and how it imports the driver at runtime.


Required Parameters
-------------------

.. Fill in the Description and Expected Value columns for every key the model
   requires. You can find these by reading driver_latest.py and tracing which
   keys forse.py accesses from the driver dictionary.

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - Parameter
     - Type
     - Description
   * - ``SITE_NAME``
     - ``str``
     -
   * - ``NX``
     - ``int``
     -
   * - ``NY``
     - ``int``
     -
   * - ``NTREES``
     - ``int``
     -
   * - ``NYEARS``
     - ``int``
     -
   * - ``NSPECIES``
     - ``int``
     -
   * - ``SPECIES_LIST``
     - ``list``
     -


Species Parameters
------------------

.. Driver files define per-species parameters, typically as lists indexed by
   species number. Describe each parameter and its units.
   Fill in the table below — add or remove rows to match the actual parameters.

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - Parameter
     - Units
     - Description
   * - ``AGEMX``
     - years
     -
   * - ``DBHMX``
     - cm
     -
   * - ``G``
     - —
     -
   * - ``HTMX``
     - m
     -
   * - ``C1``, ``C2``
     - —
     -


Climate Scenario Block
----------------------

.. Describe the return_warming_weather() function pattern used in driver files:
   - Site-specific CMIP6 parameters (DT_site, XT_site, VT_site, XR_site, VR_site)
   - The warming period (2021–2100) and piecewise linear warming rates
   - How this function is called by generate_weather() in forse.py
   - How to modify it for a different climate scenario


Optional Parameters
-------------------

.. Parameters that have defaults or can be omitted.

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - Parameter
     - Default
     - Description
   * - ``LOGGING_TREATMENT_TYPE``
     - ``None``
     -
   * - ``USE_NUMBA``
     - ``True``
     -
   * - ``VERBOSE``
     - ``False``
     -


Annotated Example
-----------------

.. Walk through a minimal but complete driver file, explaining each section.
   Use the driver_latest.py file as your reference. You can include code blocks
   with explanatory text between them, like a tutorial.

.. code-block:: python

   # Site identification
   SITE_NAME = 'your_site_name'

.. Add explanation here, then the next block, and so on.
