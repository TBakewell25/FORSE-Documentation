Basic Usage
=====

.. _installation:

Installation
------------

To use FORSE, first download the source code via the GitHub repository:

.. note::
    | There are two ways to clone the respository:
    | 1. As you would any other repository, with the standard 'git pull' command
    | or
    | 2. Recursively cloning the submodules, allowing access to the site_cache repo (recommended)
    | Both are provided as examples below.

Clone without submodules (no site_cache access)\:
.. code-block:: console

   git clone git@github.com:aharmstrong/FORSE-Spatially-Explicit-Forest-Model.git

Or, with submodules (additionally clones site_cache repo)\:
.. code-block:: console

    git clone --recurse-submodules git@github.com:aharmstrong/FORSE-Spatially-Explicit-Forest-Model.git
    


Creating recipes
----------------

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

.. autofunction:: lumache.get_random_ingredients

The ``kind`` parameter should be either ``"meat"``, ``"fish"``,
or ``"veggies"``. Otherwise, :py:func:`lumache.get_random_ingredients`
will raise an exception.

.. autoexception:: lumache.InvalidKindError

For example:

>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']

