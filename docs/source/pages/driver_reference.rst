Driver File Reference
=====================

What a Driver File Is
---------------------

In the FORSE model Python source files known as 'driver files' are used to provide both data and runtime
configuration settings to the run. The model ingests these as arguments from the command line, executes them via 
a call to the Python interpreter, and parses the Python dictionary of information that they return. 

Top-Level Parameters
--------------------

.. list-table::
   :header-rows: 1
   :widths: 40 15 45

   * - Parameter
     - Type
     - Description
   * - ``LAT``, ``LON``
     - ``float``
     - Latitude and longitude of the DEM center.
   * - ``run_description``
     - ``str``
     - Plaintext description of the run site, does not affect runtime behavior.
   * - ``TITLE``
     - ``str``
     - Model title, does not affect runtime behavior.
   * - ``sim_start_year``
     - ``int``
     - The first year of simulation.
   * - ``sim_stop_year``
     - ``int``
     - The last year of simulation.
   * - ``LIGHT_MODE``
     - ``str``
     - Must be ``"3D"``, signifying 3D ray-tracing for light simulation.
   * - ``MAX_TREES_PER_PLOT``
     - ``int``
     - The maximum number of trees an individual plot can hold.
   * - ``MAX_TREE_HEIGHT``
     - ``int``
     - The maximum height of an individual tree.
   * - ``DDBASE``
     - ``float``
     - Degree day base for growth (°C). Typically ``5``.
   * - ``PHIB``
     - ``float``
     - Fraction of solar radiation that is direct.
   * - ``PHID``
     - ``float``
     - Fraction of solar radiation that is diffuse.
   * - ``AET_RATIO``
     - ``float``
     - Ratio of Actual Evapotranspiration to Potential Evapotranspiration.
   * - ``WTD``
     - ``float``
     -
   * - ``ALLOW_REGENERATION``
     - ``bool``
     - Allow trees to regenerate stochastically.
   * - ``CLIMATE_RANDOM``
     - ``bool``
     - Setting this parameter to False results in reproducible runs. Setting it to True will make each run have random climate, as much as statistics allow.
   * - ``PERMAFROST``
     - ``bool``
     - Toggle switch for permafrost module.
   * - ``WILDFIRE``
     - ``bool``
     - Toggle switch for wildfire module.
   * - ``INSECT_OUTBREAK``
     - ``bool``
     - If true, a combination of warm spring and stressed trees result in greater mortality than SMORT & AMORT; if false, not activated.
   * - ``DEBUG``
     - ``bool``
     - Toggle for debug mode.


File Path Parameters
--------------------

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - Parameter
     - Description
   * - ``DEM_file_path``
     - The path to the input DEM, specified by the ``--dem`` flag.
   * - ``DEM_no_data``
     - Empty DEM for fallback.
   * - ``elevation_lapse_rate_adjustment_matrix_filepath``
     - The path to the DEM temperature offset data.
   * - ``monthly_radiation_files_path``
     - The path to month by month radiation data.
   * - ``Radiation_fraction_file_path``
     -
   * - ``Site_index_file_path``
     - Path to a file containing a per-plot index value.
   * - ``Site_index``
     - Value describing how well species perform at a site (1 = good, 5 = poor).


Climate Configuration
---------------------

.. The climate block is the most complex part of the driver. Describe the three-phase
   weather system: pre-MERRA2 (statistical), MERRA2 reanalysis (daily), and future
   (CMIP6 or warming scenario). Describe the 7 named options from driver_latest.py
   and when each is appropriate.

The driver supports seven climate configuration options, ranging from purely
statistical weather to hybrid MERRA2/CMIP6 approaches.

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Parameter
     - Description
   * - ``annual_weather_function_type``
     - ``"Daily"`` or ``"Stats"``. Controls pre-MERRA2 weather generation.
   * - ``return_annual_weather_function``
     - Read real data and return daily temperature and monthly precipitation.
   * - ``Real_Climate_Start_Year``
     -
   * - ``Real_Climate_End_Year``
     -
   * - ``Real_Climate_Time_Step``
     - time increment for real climate data points
   * - ``return_annual_past_weather_function``
     -
   * - ``annual_future_weather_function_type``
     -
   * - ``return_annual_future_weather_function``
     -

The ``return_warming_weather`` Function
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. Describe the warming scenario function defined at the top of the driver file
   (outside the dictionary). Explain:
   - The CMIP6 site-specific vectors: DT_site, XT_site, VT_site, XR_site, VR_site
   - The equation_factory closure pattern
   - The three phases: before warming_start_year, during warming, after warming_stop_year
   - How to update site-specific vectors (reference the Colab notebook in the comment)


Logging Treatment Parameters
-----------------------------

.. list-table::
   :header-rows: 1
   :widths: 45 55

   * - Parameter
     - Description
   * - ``LOGGING_TREATMENT_TYPE``
     - ``None``, ``"Stripcut"``, ``"Shelterwood"``, or ``"Clearcut"``.
   * - ``LOGGING_TREATMENT_FREQ``
     - Harvest interval in years.
   * - ``LOGGING_TREATMENT_START_YEAR``
     - First year of logging treatment.
   * - ``LOGGING_STRIPCUT_DIRECTION``
     - Strip direction in degrees.
   * - ``LOGGING_STRIPCUT_CLEAR_WIDTH``
     - Width (m) of cleared strip.
   * - ``LOGGING_STRIPCUT_KEEP_WIDTH``
     - Width (m) of retained strip.
   * - ``LOGGING_TREATMENT_SHELTERWOOD_DBH``
     - Eligible DBH values for logged trees.
   * - ``LOGGING_TREATMENT_SHELTERWOOD_CUT_PERCENT``
     - Percentage of trees to be logged.
   * - ``LOGGING_TREATMENT_SHELTERWOOD_CUT_SPECIES``
     - Species to be logged.


Output Control Parameters
--------------------------

.. list-table::
   :header-rows: 1
   :widths: 45 55

   * - Parameter
     - Description
   * - ``log_this_year_function``
     - Lambda ``(year) -> bool``. Controls which years are written to HDF5.
   * - ``export_solar_radiation_year_function``
     - Lambda ``(year) -> bool``. Controls what solar radiation data is written.


Soil Parameters
---------------

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Parameter
     - Description
   * - ``Sand``
     - Sand percentage of soil.
   * - ``Clay``
     - Clay percentage of soil.
   * - ``FC``
     -
   * - ``WP``
     -
   * - ``soil["Od"]``
     - O horizon depth (organic/humus layer).
   * - ``soil["Ad"]``
     - A horizon depth (mineral layer).
   * - ``soil["itxt"]``
     - Soil texture: ``1`` = granular, ``2`` = fine-textured.
   * - ``soil["o_horizon_fc"]``
     -
   * - ``soil["a_horizon_fc"]``
     -


Species Parameters
------------------

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Parameter
     - Description
   * - ``ENABLED``
     - ``bool``. Set to ``False`` to exclude the species from the simulation.
   * - ``STRESS_THRESHOLD``
     - Fraction of optimal growth that must be achieved to avoid a stress flag (0–1).
   * - ``SEED``
     - Relative seed abundance (0.1, 0.31, or 0.56 based on occurrence).
   * - ``DMAX``
     - Maximum trunk diameter (cm).
   * - ``BIOVOLUME_EQUATION``
     - ``function(dbh) -> float``. Returns biovolume in m³.
   * - ``BIOMASS_EQUATION``
     - ``function(dbh) -> float``. Returns total biomass in Mg.
   * - ``LEAF_AREA_EQUATION``
     - ``function(dbh) -> float``. Returns leaf area in m².
   * - ``AGE_MORTALITY_EQUATION``
     - ``function() -> float``. Returns annual age-mortality probability (0–1).
   * - ``BASAL_AREA_EQUATION``
     - ``function(dbh) -> float``. Returns basal area in m².
   * - ``OPTIMAL_GROWTH_INCREMENT_EQUATION``
     - ``function(dbh) -> float``. Returns optimal annual DBH increment (cm).
   * - ``TREE_HEIGHT_EQUATION``
     - ``function(dbh) -> float``. Returns tree height in m.
   * - ``SOIL_FERTILITY_FACTOR_EQUATION``
     - ``function(fertility) -> float``. NUTRI: 1 = intolerant, 3 = tolerant.
   * - ``SOIL_MOISTURE_FACTOR_EQUATION``
     - ``function(dry_days) -> float``. MDRT: 1 = dry-intolerant, 5 = tolerant.
   * - ``PERMAFROST_FACTOR_EQUATION``
     - ``function -> float``. PFTOL: 1 = not inhibited, 2 = inhibited by permafrost.
   * - ``DEGREE_DAY_FACTOR_EQUATION``
     - ``function(GDD) -> float``. Requires DDMIN and DDMAX (°C·days).
   * - ``AVAILABLE_LIGHT_FACTOR_EQUATION``
     - ``function(light) -> float``. LIGHT: 1 = shade tolerant, 5 = intolerant.
   * - ``LIGHT_COMPENSATION_POINT``
     - Minimum light level for positive growth (0–1).
   * - ``INSEEDING_LAG``
     - Delay in years before seedlings of this species can establish.
   * - ``WATER_LOG``
     - Response function to waterlogging conditions.
   * - ``CROWN_BASE_MAX_PERCENT``
     - Maximum crown base height as fraction of total height. Conifers: 0.533; deciduous: 0.467.



