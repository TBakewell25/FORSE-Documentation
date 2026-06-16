Getting Started
=====

.. _install:

Install
------------

To use FORSE, first download the source code via the GitHub repository:

.. note::
    | There are two ways to clone the respository:
    |     1. As you would any other repository, with the standard 'git pull' command
    |
    | or
    |
    |     2. Recursively cloning the submodules, allowing access to the site_cache repo (recommended)
    | Both are provided as examples below.

Clone without submodules (no site_cache access):

.. code-block:: console

   git clone git@github.com:aharmstrong/FORSE-Spatially-Explicit-Forest-Model.git

# Or, with submodules (additionally clones site_cache repo):

# .. code-block:: console

#    git clone --recurse-submodules git@github.com:aharmstrong/FORSE-Spatially-Explicit-Forest-Model.git
    
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

Pip fills a similar gap as Conda, that is providing a runtime environment for the model, but is Python's built-in package manager and is therefore available on any machine which has 
a Python installation. The workflow for building the environment is not dissimilar from that of Conda, but now we rely on the ``requirements.txt`` as the basis for building our environment.
First create a new environment:

.. code-block:: console

    python -m venv <environment-name>

This command creates a blank virtual environment. To activate it use the command:

.. code-block:: console 

    source ./<environment-name>/bin/activate

Then install the required dependencies:

.. code-block:: console

    pip install -r requirements.txt

This command will only need to be run the first time you activate your virtual environment, the dependencies will be saved and accessible every time you access the environment in the future.
After you are finished using the virtual environment it can be quickly deactivated by using:

.. code-block:: console

    deactivate

.. note::

    A potential source of friction with the in using Pip for FORSE's dependency management is the reliance of GDAL, a crucial library, on system resources. 
    That is to say, the version of GDAL Pip has in its repositories expects the user to have some base GDAL installation already available on their machine, 
    and will not work if that is not the case. If installation issues arise surrounding GDAL consider either installing the base library to your machine manually,
    or switching to Conda.





