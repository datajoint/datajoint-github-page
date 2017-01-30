Frequently Asked Questions
**************************

What kind of data can DataJoint accommodate?
============================================
DataJoint is a framework that helps organize data using the relational data model. It can accommodate any data. In its most common use, data are inserted from MATLAB, so if it can be stored in a MATLAB variable, it can be stored in DataJoint.

Is DataJoint ready?
===================
.. _`here`: https://github.com/datajoint/datajoint-matlab/issues

The MATLAB and Python versions of DataJoint are mature. Any outstanding issues can be viewed or submitted `here`_


How big are the datasets that can be handled with DataJoint?
============================================================

.. _`MySQL documentation`: http://dev.mysql.com/doc/refman/5.6/en/limits.html

DataJoint uses MySQL's InnoDB engine as its back end. The data size limits can be found in the `MySQL documentation`_. For example, the maximum table space managed by the InnoDB engine (DataJoint's default) is 64 TiB, but the data are typically distributed across multiple tables.

The daily output of our lab may be on the order of hundreds of GB of raw data. The processing chain extracts only the essential data and imports it into the database. Still, even the essential data may exceed tens of GB per day.

Do you store raw data in the database?
======================================

.. _`aod.TraceSet`: https://github.com/atlab/sessions/blob/master/schemas/%2Baod/TraceSet.m

Typically not. The acquisition software writes out the raw data into files. The DataJoint processing chain then imports the essential data from raw files. For example, the class `aod.TraceSet`_ extracts the fluorescence traces for a set of cells in an AOD-based two-photon scan. The raw scan data is read from an external file and only the significantly smaller result of the computation is stored in the database (See https://github.com/atlab/sessions).

 However, it's up to the user to decide which data go into the database. Since DataJoint uses regular MySQL tables to store data, other software can be written to write data directly into the database bypassing DataJoint altogether.

What alternative systems did you consider and why did you end up writing your own system?
===========================================================================================
In many labs, the default data management scheme comprises the OS file system with clever naming conventions with additional meta data stored in CSV, XML, .MAT files, for example. This works fine for dead storage, but makes more interesting queries difficult.  Also, data dependencies are difficult to maintain.

Another alternative is to use a relational database system (e.g. MySQL) using SQL. However, SQL queries are typically cumbersome. SQL requires significant training to be used effectively. DataJoint makes using the proper relational data model easier.

DataJoint met our need for a flexible, light-weight, yet capable system that is easy to learn and use.
