SMCE Instructions
=================

The following instructions apply to users running the FORSE model on NASA's Science Managed Cloud Environment (`SMCE <https://sciencecloud.nasa.gov/overview/>`_)
server.

Access SMCE
-----------

SMCE resources are accessible to those with approved credentials via the `online portal <https://hub.airborne.smce.nasa.gov>`_.
SMCE compute resources are allocated via on demand, virtualized environments relying on an AWS background. For material regarding
appropriate usage of SMCE resources and further tutorials for new users please consult the `SMCE documentation <https://airborne-smce.readthedocs.io/en/latest/>`_

The SMCE FORSE Environment
--------------------------

The full FORSE environment, the contents and purpose of which are described in other sections of this literature, is available to all users via a 
global Conda-Store environment accessed by two entrypoints: the `Notebook` interface, and the `Console` interface.

.. image:: ../images/smce/environ.png

The `Notebook` provides the user with the familiar Jupyter Notebook interactive interface, allowing the combination of markup text and runnable Python code. 
The `Console` acts as a standard Python REPL (**R**\ead **E**\valuate **P**\rint **L**\oop), being designed for line by line execution of code by a Python interpreter. Most users
will find more value in the `Notebook`, as it allows for more complete scripting and is intended for work that will persist.

Additionally, a full terminal environment is available. This allows the user to interact directly with FORSE in the same was as described in :doc:`pages/usage`. Users working in the terminal
will need to manually activate the Conda environment to run the model.

Interacting With FORSE in SMCE
------------------------------



