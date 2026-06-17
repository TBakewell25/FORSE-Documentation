The Simulation Loop
===================

.. This page walks through what happens inside forse.py from start to finish.
   The goal is that a developer reading this can follow the code without getting lost.


Startup and Initialization
---------------------------

.. Describe what happens before the main loop begins:
   - load_driver_py() loads the configuration dictionary
   - The DEM is read in via GDAL, establishing the nx × ny plot grid
   - 3D matrices are allocated (DBH_matrix, species_code_matrix, etc.)
   - Light "arrows" (ray directions + solar proportions) are pre-computed once
   - The HDF5 output file is opened
   Mention the initialization() function in forse.py.


The Annual Loop
---------------

.. Describe the year-by-year structure of the simulation.
   Each subsection below is one step in the loop. Fill in what each step does,
   which function(s) it calls, and what data it reads/writes.

Weather Generation
^^^^^^^^^^^^^^^^^^

.. generate_weather() — monthly temperature and precipitation matrices,
   lapse rate adjustment from DEM elevation, GDD accumulation, dry day fraction.

Tree Mortality
^^^^^^^^^^^^^^

.. kill_trees() — two mortality mechanisms: age-based (species max age) and
   stress-based (accumulated stress flags). Describe how a tree is "removed."

Individual Tree Values
^^^^^^^^^^^^^^^^^^^^^^

.. compute_individual_tree_values() — converts DBH to height (via allomlib),
   then to leaf area, biomass, and basal area. Explain why this runs before light.

3D Leaf Area
^^^^^^^^^^^^

.. compute_actual_leaf_area() — distributes each tree's leaf area into the
   3D voxel grid, which is the input to the light calculation.

Light Calculation
^^^^^^^^^^^^^^^^^

.. compute_light() → light3d.py — see :doc:`simulation` for full detail.
   Brief mention here: Beer-Lambert attenuation through the 3D leaf area matrix.

Growth Factors
^^^^^^^^^^^^^^

.. compute_species_factors_weather() and compute_species_factors_soil() —
   each returns a per-species, per-plot factor matrix (values 0–1).
   Describe what conditions drive each factor toward 0 (growth limitation).

Crown Base
^^^^^^^^^^

.. compute_crown_base() — crown recession as a function of light availability.
   Self-pruning of shaded lower branches.

Light Factor
^^^^^^^^^^^^

.. light_factor_compute() — species-specific response to available light at
   crown base. Shade-tolerant vs. intolerant species behave differently here.

Tree Growth
^^^^^^^^^^^

.. grow_trees() — the multiplicative growth model. Explain the formula:
   DBH increment = optimal increment × GDD factor × drought factor ×
   light factor × soil factor. Describe how stress flags are updated.

Regeneration
^^^^^^^^^^^^

.. sprout_saplings_v2_3() — two pathways: seedling establishment (ground
   light dependent) and stump sprouting from recently harvested trees.

Logging Treatment
^^^^^^^^^^^^^^^^^

.. log_trees() — only runs if LOGGING_TREATMENT_TYPE is set in the driver.
   Describe how harvested trees are marked (DBH = 0, species = -1).

Annual Output
^^^^^^^^^^^^^

.. Describe what is written to the HDF5 file each year: per-plot, per-species
   tables of DBH, species code, basal area, biomass, stem density.
   Mention that the driver file itself is stored in the output for reproducibility.


The Growth Model
----------------

.. Expand on the multiplicative factor model introduced above.
   Explain the design philosophy: each environmental stress independently limits
   growth, and factors multiply together (not add). Discuss the biological
   justification and any known limitations of this approach.


Light Calculation in Detail
----------------------------

.. Describe how light3d.py works:
   - "Arrows" represent rays of sunlight from different solar angles
   - Each arrow has a direction vector and a proportion of total radiation
   - Beer-Lambert law: light attenuates exponentially through leaf area
   - The extinction coefficient XK = 0.4
   - Numba is used because this is an O(n³) computation over the 3D grid
   Explain why this is the most computationally expensive step in the loop.
