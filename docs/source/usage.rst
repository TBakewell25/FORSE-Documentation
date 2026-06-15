Getting Started
=====

.. _installation:

Installation
------------

To use FORSE, first download the source code via the GitHub repository:

.. note::
    | There are two ways to clone the respository:
    |     1. As you would any other repository, with the standard 'git pull' command
    | or
    |     2. Recursively cloning the submodules, allowing access to the site_cache repo (recommended)
    | Both are provided as examples below.

Clone without submodules (no site_cache access):

.. code-block:: console

   git clone git@github.com:aharmstrong/FORSE-Spatially-Explicit-Forest-Model.git

Or, with submodules (additionally clones site_cache repo):

.. code-block:: console

    git clone --recurse-submodules git@github.com:aharmstrong/FORSE-Spatially-Explicit-Forest-Model.git
    
Building the Run Environment
----------------------------

Running the FORSE model requires building a runtime environment for the many dependencies. Python environments 
provide a way to install many library packages (NumPy, Matplotlib, etc.) without disturbing the host system (i.e 
your computer). The FORSE environment is buildable both via Conda and Venv, although Conda is more common in practice.

Build With Conda
----------------

First, `Conda <http://www.anaconda.org>`_ must be installed as a sytem wide package. Conda (or 'anaconda', depending on your installation)
provides the aforementioned environment for running FORSE. It is possible that Conda is already installed on your system if you are not using
your personal machine, consult your system administrator if you are unsure.

Within the root directory of the FORSE repository is a file ``environment.yml``. The contents of this file are a plain-text list of all the dependencies
required by the FORSE environment, which Conda will use as reference. Use the following command to build a Conda environment from this template:

.. code-block:: console

    conda env create -f environment.yml

This command should initiate the installation of this environment. Upon completion, you should see this environment, ``forse``, listed among your installed environments 
when you use the following command:

.. code-block:: console

    conda env list

Now, having installed the environment to Conda, you must activate it:

.. code-block:: console

    conda activate forse

Build With Pip
--------------

