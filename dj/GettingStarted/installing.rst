Installing DataJoint
^^^^^^^^^^^^^^^^^^^^

.. toctree::
   :titlesonly:
   :hidden:

   self

Installing DataJoint for Matlab
*******************************

How do I install the Matlab DataJoint library?

Matlab specific prerequirements
===============================

.. _`mYm library`: https://github.com/datajoint/mym
.. _`sourceforge`: http://sourceforge.net/projects/mym/
.. _`Matlab Central`: http://www.mathworks.com/matlabcentral/linkexchange/links/1313-mysql-connector-mym
.. _`latest matlab release`: https://github.com/datajoint/datajoint-matlab/releases

* MATLAB R2011a or higher.  Earlier versions can be used with a few modifications.
* `mYm library`_ used as the low-level MySQL API.  The original project is at sourceforge_ and at `Matlab Central`_.


Matlab install steps
====================
.. _`startup.m`: http://www.mathworks.com/help/matlab/ref/startup.html

#. Download the `latest matlab release`_ of datajoint and unzip it.
#. Place the unzipped folder somewhere where it won't be deleted.
#. Edit `startup.m`_ to add the datajoint directory to the search path and execute `<datajoint-path>/mym/mySetup.m`.

.. code-block:: matlab

  % startup.m
  addpath <datajoint path>
  run <datajoint-path>/mym/mymSetup


At the MATLAB command prompt, test that DataJoint and mym are on your path:

.. code-block:: matlab

  >> mym version
  mYm version 2.6.8
  >> which dj.conn
  /<datajoint-path>/+dj/conn.m


To use DataJoint you need access to a MySQL server. See :ref:`Configuring a Connection`.


Installing DataJoint for Python
*******************************

"How do I install the Python DataJoint library?"

Python specific prerequirements
===============================

.. _`anaconda`: http://continuum.io/downloads

DataJoint is written for python 3.4. For Mac OS X and Windows users, we recommend the anaconda_ distribution.

Additionally, you will need the following libraries

* numpy
* pymysql
* matplotlib
* json
* pandas
* networkx

Python Install Steps
====================

Run

.. code-block:: bash

  pip install git+https://github.com/datajoint/datajoint-python


.. _`latest release`: https://github.com/datajoint/datajoint-python/releases


Alternatively,

#. Download the `latest release`_ of datajoint and unzip it.
#. ``CD`` into the folder and run

.. code-block:: bash

  python setup.py install
