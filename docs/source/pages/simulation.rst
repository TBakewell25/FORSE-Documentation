The Simulation Loop
===================

.. This page walks through what happens inside forse.py from start to finish.
   The goal is that a developer reading this can follow the code without getting lost.
This page describes in greater depth what happens when the model is called. It includes 
more detailed descriptions of the driver file architecture, the order in which simulation components
are performed, and a few references to specific code segments. It aims to provide a simple yet effective
explanation on how to look at the model.

Startup and Initialization
---------------------------

.. Describe what happens before the main loop begins:
   - load_driver_py() loads the configuration dictionary
   - The DEM is read in via GDAL, establishing the nx × ny plot grid
   - 3D matrices are allocated (DBH_matrix, species_code_matrix, etc.)
   - Light "arrows" (ray directions + solar proportions) are pre-computed once
   - The HDF5 output file is opened
   Mention the initialization() function in forse.py.

Running the Driver
^^^^^^^^^

The FORSE model, as described previously, is built around the driver architecture. The driver provides data and configuration
settings that are necessary for each run. 

The driver might at first seem to be a generic configuration file, but it is important to note that it itself is actually Python source.
FORSE does not simply parse it as data, it reads in its contents and executes them as Python, allowing for the resulting dictionary to
be built and accessible in the same scope as ``main()`` (or any other function in ``forse.py``). The following line covers this:

.. code:: python
    exec(open(driver_file).read())

The ``open().read()`` combination accesses the contents of the ``.py`` driver as raw text data. Having read it in, ``exec()`` instructs the 
interpreter to run the code. 

Building Data Structures
^^^^^^^^^

FORSE uses the **G**\eospatial **D**\ata **A**\bstraction **L**\ibrary (`GDAL <https://gdal.org/en/stable/>`_) for parsing DEM data. GDAL is
a very common open source tool for remote sensing and earth science applications. DEM files are specified in the driver file, and parsed into a
NumPy matrix during the initialization process, being accessible by the key ``DEM_mat`` within the driver dictionary.

Additional 3D tensors are packed into the driver dictionary using source data files. 3D matrices are usually (plot X dimensions) x (plot Y dimensions) x (specified metric).
For instance, the species code matrix is (X dimension) x (Y dimension) x MAX_TREES_PER_PLOT, where each column is a list of tree species in that plot. All of these data structures
are stored in the driver dictionary.






The Annual Loop
---------------

.. Describe the year-by-year structure of the simulation.
   Each subsection below is one step in the loop. Fill in what each step does,
   which function(s) it calls, and what data it reads/writes.

At the top level FORSE collects and writes data on an annual basis. Beginning with the first year specified in the driver file, and ending with some final year again provided to the driver,
the model steps through incrementally and models forest dynamics. This process can begin no earlier than the completion of initialization, and is the most resource intensive part of execution.

Weather Generation
^^^^^^^^^^^^^^^^^^

Part of the simulation is monthly weather generation provided by the routines available in ``weather.py``. The top level interface for this module, ``generate_weather()`` builds the following data sets:
   
    * **Temperature**\: Temperatures are generated for each day in a year using a truncated Gaussian distribution based on historical averages, and packed into monthly temperature matrices.
    * **Precipitation**\: Just as with temperature, another distribution is used to generate a monthly precipitation matrix.
    * **Growing Degree Days**\: Multiple routines are available within the weather module for calculating GDD.
    * **Dry Day Fraction**\: Dry day fraction is calculated as the number of cumulative "dry days" divided by the number of days within the growing season for the year. The dry day accumulator matrix is itself
                                build using the ``soil_moisture()`` function applied to monthly data.
    
Additionally worth noting is the following:

    * **Lapse Rate Adjustments**\: FORSE is a spatially explicit model, and therefore takes special care to model the environment it simulates with a high degree of accuracy and specificity. The weather module
                                    makes use of baseline averages for monthly temperatures which it then modifies with the lapse rate adjustment matrix to create plot by plot temperature matrix that 
                                    reflects the variability in temperature caused by the surface elevation and aspect. 

Tree Mortality
^^^^^^^^^^^^^^

A crucial mechanism of our gap model is the ability to remove dead trees. Weather by natural causes, or by human intervention, the death of trees and the change of the canopy is crucial to forest
dynamics. Trees die in FORSE by two mechanisms: **age-based** and **stress-based**. We see these processes come into play in the ``kill_trees()`` function.

A few metrics are considered when deciding whether or not an individual tree will die. First, we reference the individual stress flag for each tree, which accumulates as the tree experiences sub-optimal
conditions. For **stress-based** mortality if the stress flag is greater than two, and a pseudo-random number associated with the tree is less than approximately 0.37, the tree is dead and it is zeroed 
out in the DBH matrix. For age-based mortality we check if a separate pseudo-random number associated with the tree is less than the tree's age based mortality probability, which we have derived from 
``AGE_MAX`` in the driver file, and if it is we again zero out the DBH bookkeeping.

This work is done in parallel using Numba, and returns updated shallow copies of the aforementioned data structures.

Individual Tree Values
^^^^^^^^^^^^^^^^^^^^^^

.. compute_individual_tree_values() — converts DBH to height (via allomlib),
   then to leaf area, biomass, and basal area. Explain why this runs before light.
The ``compute_individual_tree_values()`` from the ``create_specialized_driver_code.py`` applies species specific allometric equations from the driver to derive the following values, each of which
is packed into a plot-wise matrix:

        * individual height (m)
        * individual total leaf area (m^2)
        * individual biomass (kg)
        * optimal growth increment (cm)
        * individual optimal biomass (kg)
        * individual basal area (m^2)
        * individual biovolume (m^3)
        * individual optimal biovolume (m^3)
        * individual optimal biovolume increment (m^3)
        * water logging factor (unitless)

The ``create_specialized_drive_code.py`` file uses ``Mako``, a Python library that templates its contents, which exist largely as standard Python strings, into runnable Python code. Reading through
this file (which is heavily recommended for better understanding the driver architecture) you will see many code blocks that feature ``$`` and ``%`` characters. These are used at runtime to provide 
Mako with targets where it will substitute in values specified by the driver file and then run the code. In short, we are using the driver file to configure custom behavior in our Python code, and then running
it.

For ``compute_individual_tree_values()`` we are, via the templating system, assigning each tree species a number and calling the requisite allometric functions associated with it. The driver file
contains these specific functions, Mako allows us to design an architecture that is able to call them without knowing what they will be or how many of them will exist until runtime substitution.
This is crucial to the modularity of the model, and is the best justification for the driver file architecture over a simple plain-text configuration file.

3D Leaf Area
^^^^^^^^^^^^

Leaf area calculation is another example of a model parameter that is generated at runtime dynamically by way of the driver file. Individual species leaf area enters the model via 
the aforementioned ``compute_individual_tree_values()`` function, being packed into the matrix ``total_leaf_area_matrix`` which has a plot-by-plot tree-by-tree representation of leaf area.

The function ``compute_actual_leaf_area()`` calculates leaf density (leaf area / tree height) for each individual, and accumulates this value for each increment in the foliage column for that 
tree. That is to say, for each elevation increment in an individual tree the density at that elevation is added to a running total for that plot. This comes to form an important input for
light calculation.

Light Calculation
^^^^^^^^^^^^^^^^^

.. compute_light() → light3d.py — see :doc:`simulation` for full detail.
   Brief mention here: Beer-Lambert attenuation through the 3D leaf area matrix.



Growth Factors
^^^^^^^^^^^^^^

Growth factors are used to represent the extent to which external conditions limit the growth of species in the model. The function ``compute_species_factors_weather()``, another driver
function, provides factor matrices for two cases: growing degree days and soil moisture.

Using the species specific functions built by the driver file, this function uses existing GDD and soil moisture matrices to calculate on a per-species basis how the growth of a tree will be limited
by environmental factors. The end product is a 3D tensor of values between 0 and 1, where each entry represents a specific tree on a specific plot. When annual biomass increment is calculated these 
matrices become relevant, as the total increment value is scaled using them. Consult extant driver files for examples of species specific implementations of calculating these factors.

Crown Base
^^^^^^^^^^

The model uses ``compute_crown_base()`` to compute crown recession as a function of light availability. Every tree has a compensation point (CP), specified in FORSE by a set of species specific
values in the driver, the point at which the energy gained by photosynthesis equals the energy expended on respiration and metabolism, and the growth rate is 0. Branches below the compensation point,
that is branches that receive so little light that they are a drain on resources are pruned, thus changing the crown base.

For this process an iterative loop steps down in 1 meter increments from the top of the tree to its current crown base, resetting the crown base if it ever encounters a layer where available light is equal
to or below the CP. Available light is taken directly from the ``available_light_mat``, and CP is again provided by the driver file. After each run of this function an updated crown base matrix is provided.

Light Factor
^^^^^^^^^^^^

As mentioned above in the discussion of growth factors, the availability of light, or light factor, must be calculated for each plot to determine the incremental growth the trees there experience.
For an individual tree in the stand its light factor is computed as the average of its species light factor over its height. For each layer between the crown base and the top of the tree the associated 
light factor for that species is summed, and the final value is divided by the total tree height, giving the averaged value of light factor for that individual.

Also computed is ground light factor, the amount of light which reaches the ground. This value is simply the light factor for each species at z=0 (0 elevation).

The light factor helps divide shade-tolerant from shade-intolerant species. Species which are more robust against lower amounts of light end up with higher light factors, while species which are more 
reliant on light end up with lower light factors. The end product is the growth of these shade-intolerant species is restricted relative to their more rugged shade-tolerant counterparts. 

Tree Growth
^^^^^^^^^^^

.. grow_trees() — the multiplicative growth model. Explain the formula:
   DBH increment = optimal increment × GDD factor × drought factor ×
   light factor × soil factor. Describe how stress flags are updated.

In FORSE tree growth is manifested directly by changes in diameter breast height (DBH). The function ``grow_trees()`` accepts a growth increment as well as many of the aforementioned factors to determine 
how DBH changes annually for an individual tree. The final increment is expressed as **optimal increment** × **GDD factor** × **drought factor** × **light factor** × **soil factor**. 

Optimal growth increment (OGI) is calculated in the driver-reliant ``compute_individual_tree_values()`` function, making use of species specific allometric equations. See **Growth Factors** above for
information regarding GDD factors. See **Light Factor** above for information regarding light factor.

Regeneration
^^^^^^^^^^^^

The regeneration logic represents the counterbalance of the tree pruning logic. Whereas stressed or logged trees are removed from the stand, areas with optimal growing conditions sprout new trees which 
become part of the annual cycle. The ``sprout_saplings_v2_3()`` function adds new saplings to individual grid squares which will in turn continue to grow into mature trees. Regeneration occurs in a 
three-phased approach:

**1. Seed Bank Updates**

New sprouts are chosen through a structured, but randomized process. A large tensor, the seed bank, stores the seed factor values. The seed bank is four dimensions: the lag year, the species code, and the 
x and y of the plot. The novel variable here is lag year. In order for a species to be eligible as a transplant candidate it must first move through a set number of lag years to reach maturity. The number 
of requisite lag year before a seed weight is elligible for use in regeneration is specified on a species-by-species basis in the driver file. Every iteration of the annual cycle the seed weight for a 
species and plot is moved forward an additional lag year to accrue maturity, unless a sprout factor of 0 is observed, in which case all seed bank entries for that species on that plot are zeroed out. For
each species being modeled its corresponding lag entry is checked in the seed bank, and if it is non-zero the corresponding seed factor is recorded.

**2. Dartboard Species Selection**

Each iteration the function pulls the transplant count (seed bank entry) at the lag requirement for each species. These values are not literal sappling counts, but rather continuous values less than one 
which will describe the dominance of that species relative to others in regeneration. A rough tree capacity per plot is estimated, describing an upper bound for how many new trees may grow on a plot at 
any given iteration. The collected transplant counts (one for each species) are normalized together to the range [0,1], distributing them as probabilities who's frequency is scaled relative to the magnitude 
of their original seed bank value. For each of the tree capacity spaces in the slot a value is randomly sampled from the [0,1] range and used to select a tree species.

**3. Planting**

With a species probabilistically selected, book keeping must now be performed to register it as having sprouted. An entry is placed in a free spot in the DBH matrix, with its value being hardcoded as a 
random sampling from a Gaussian centered at 2.5cm. The corresponding species code, crown base, and stress flags are set for the tree (with the latter two being 0). The specific location of the tree is 
calculated using `Delaunay triangulation <https://en.wikipedia.org/wiki/Delaunay_triangulation`_ to find the largest available gap, and the birth year for the tree is recorded.


Logging Treatment
^^^^^^^^^^^^^^^^^

.. log_trees() — only runs if LOGGING_TREATMENT_TYPE is set in the driver.
   Describe how harvested trees are marked (DBH = 0, species = -1).
Logging logic only runs if the ``LOGGING_TREATMENT_TYPE`` switch is set in the driver, and is not necessary for FORSE to run. When a treatment type is specified the ``log_trees()`` function will run
to prune trees accordingly. There are three specific treatement types for the logging:

    * **Shelterwood**\: Shelterwood logging is a method wherein mature trees are harvested with priority, leaving younger healthy specimen to continue growing. At the beginning of every logging cycle 
                        the cumulative DBH is calculated for each plot for each tree of the target species, being recorded plot-by-plot in ``basal_area_matrix``. A separate matrix, ``basal_area_all_matrix``
                        records the cumulative basal area of all species for later reference. 

                        All trees in the stand are sorted by ascending basal area from the ``basal_area_matrix``, leaving non-target 
                        species as 0, and the target species sorted from smallest target to largest target. We iterate across this list in reverse so that we encounter the largest trees of the target 
                        species first.

                        Beginning with the largest specimen, trees are searched with some distance ``OPPORTUNITY_CUT_DISTANCE`` (3D Euclidean distance) and logging masks are created. Two conditions decide 
                        whether a tree is eligible for logging: whether or not it is stressed enough (stress_flag > OPPORTUNITY_CUT_MIN_STRESS), or whether these neighbor trees are large enough (DBH > min_dbh).
                        For all trees within this mask (which is always modified to include the initial base tree) their ``DBH``, ``species_code``, ``stress_flag``, 
                        ``crown_base``, and ``tree_position`` are zeroed out to signify their being pruned. 

                        This process continues until the total removed basal area, which is tracked across iterations, passes some threshold which is a percentage of the total. 

                        In shelterwood logging we undergo the above process three times: two initial times with ``OPPORTUNITY_CUT_MIN_DBH`` as the harvesting threshold, and a third time with ``SHELTERWOOD_DBH[2]``,
                        a potentially different value, as threshold.
                     
    * **Stripcut**\: Stripcut logging removes all trees within parallel strips of a specified width while leaving the intervening strips intact. Unlike shelterwood, it is purely geometric — no
                     species or size targeting is performed.

                     Before any cut logic is applied, each tree's within-plot local position is converted to absolute stand coordinates using the GDAL geotransform read from the DEM file.
                     This ensures the strip geometry is consistent across the entire stand rather than being computed independently per plot.

                     A 1D projection of each tree's position is then computed by taking the dot product of its absolute coordinates with a unit vector defined by ``LOGGING_STRIPCUT_DIRECTION``
                     (in degrees). This collapses the 2D stand into a single axis perpendicular to the strip boundaries, reducing the problem to one dimension.

                     Two boolean masks are derived from sine waves of period ``T = LOGGING_STRIPCUT_CLEAR_WIDTH + LOGGING_STRIPCUT_KEEP_WIDTH``, one shifted relative to the other by half the
                     difference between the cut and keep widths. Combining these with OR (when the cut width is greater than or equal to the keep width) or AND (when it is smaller) produces a
                     repeating pattern of cut strips of width ``LOGGING_STRIPCUT_CLEAR_WIDTH`` separated by retained strips of width ``LOGGING_STRIPCUT_KEEP_WIDTH``, running across the full
                     stand in the specified direction. All trees falling within the cut strips have their ``DBH``, ``species_code``, ``stress_flag``, ``crown_base``, and ``tree_position``
                     zeroed out.

    * **Clearcut**\: Clearcut logging is the simplest of the three treatments. All trees in the stand are removed unconditionally in a single pass — no species targeting, no size threshold,
                     and no spatial geometry. Every entry in ``DBH_matrix``, ``species_code_matrix``, ``stress_flag_matrix``, ``crown_base_matrix``, and ``tree_position_matrix`` is set to
                     zero (with ``species_code`` set to ``-1`` to indicate an empty slot). The driver is passed to the function to allow for future extensions, such as leaving small-diameter
                     stems, but no such condition is currently implemented.



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
