Tables
******



Table Types
===========

Every table in a DataJoint schema is assigned one of the four tiers: `lookup`, `manual`, `imported`, or `computed`.  The tier helps establish the basic role of the data in the table and how valuable (difficult to replace) these data are. In Python, the tier is specified by inheriting from `dj.Manual`, `dj.Computed`, `dj.Lookup`, or `dj.Imported`. Additionally, one can mix in inheritance from `dj.Subordinate` to indicate that the table is a subtable.

**Lookup** tables contain information that is involved in setting up the processing chain itself: filter settings, algorithm parameters, etc. Lookup tables are populated manually and their contents are copied when a new schema is set up.

**Manual** tables contain information about experiments that are entered manually or by other software outside of the DataJoint processing chain. Due to their high value and small size, manual tables are backed up most frequently (e.g. everyday or after every experiment).

**Imported** tables contain information that is computed automatically by the DataJoint processing chain but requires access to external files outside the database (e.g. raw recordings). Raw recordings are typically in large files that cannot be kept online indefinitely. Once the extraction algorithms have stabilized and the extracted data have been imported into the database, the raw recordings may be taken offline. Imported tables need to be backed up regularly, especially after the raw data files have been taken offline.

**Computed** tables are populated automatically with data obtained from other tables within the database. Computed tables do not typically need to be backed up at all unless the computation time far exceeds the effort of restoring from backups.

Declaring Tables
================

The part that defines the structure of the table is the at the top of the files in Matlab and the class variables `definition` in Python. The syntax is the same in both cases. Let's look at it in more detail.

.. code-block:: bash

	subject_id                   : int                     # id number
	---
	first=""                     : varchar(20)             # first name
	last=""                      : varchar(20)             # last name
	sex="unknown"                : enum('M','F','unknown') # animal's sex
	subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic


* **comments and empty** Everything after a `#` is ignored. Empty lines are ignored as well.
* **table identity** Table identity is defined differently in Matlab and Python.

In Matlab, the first non-blank line identifies the table as follows:

.. code-block:: matlab

	<package>.<TableName>(<tier>)   # comment about the table


`<package>` denotes the name of the package in Matlab. `<TableName>` must match the name of the class. `<tier>` can be one of the following: `lookup`, `manual`, `imported`, or `computed`.

In Python, the table name is defined via the class name and the tier is defined by what this class inherits from (`dj.Manual`, `dj.Computed`, `dj.Lookup`, `dj.Imported`). That leaves only the comment to be specified by the first line in the table definition

.. code-block:: python

	@schema
	class MyClass(dj.tier):
		definition = """
		# comment about the table
		...
		"""


The rest of the declaration defines table attributes. The declaration is separated into two parts separated by a line that starts with at least three minuses dashes ``---``. Below we cover:

* Primary keys
* Foreign keys
* attributes
* indexes

Primary keys
============
All attributes above the separator make up the primary key. Primary keys uniquely identify a row (tuple) in a table. There can be no two tuples with the same set of primary key values in a table.

What's a primary key?
---------------------
Each table must have exactly one [primary key](http://en.wikipedia.org/wiki/Primary_key): a subset of its attributes that uniquely identify each tuple in the table.  The database uses the primary key to prevent duplicate entries, to relate data across tables, and to accelerate data queries. The choice of the primary key will determine how you identify tuples. Therefore, make the primary key **short**, **expressive**, and **persistent**.
For example, mice in our lab are assigned unique IDs. The mouse ID number of type `smallint` can serve as the primary key for the table `Mice`.

Datatypes in primary keys
-------------------------
All integer types, dates, and timestamps make good primary key attributes. String (`char` and `varchar`) are less common because they can be quite long and because they may have invisible trailing spaces. Floating-point numbers should be avoided because rounding errors may lead to logical errors. Enums are okay as long as they do not need to be modified later when [[foreign keys]] are already created referencing the table. Finally, dataJoint does not support blob types in primary keys.

The primary key may be composite, i.e. comprising several attributes. In DataJoint, hierarchical designs often produce tables whose primary keys comprise many attributes.

Natural primary keys
--------------------
A primary key comprising real-world attributes is a [natural primary key](http://en.wikipedia.org/wiki/Natural_key). Natural primary keys are a good choice when such real-world attributes are already properly assigned and their permanence is ensured.  If no convenient natural key exists, you may choose to introduce a [surrogate primary key](http://en.wikipedia.org/wiki/Surrogate_key), i.e. an artificial attribute whose purpose is to uniquely identify tuples in a table.  An institutional process must ensure the uniqueness and permanence of a surrogate key. For example, the U.S. government assigns every worker a surrogate attribute, the social security number (SSN), but the government must go to great lengths to ensure that this primary key is assigned exactly once by checking against other less convenient candidate keys (i.e. the combination of name, parents' names, date of birth, place of birth, etc.)  Just like the SSN, well managed surrogate keys tend to get institutionalized and, overtime, become natural.

Foreign keys
============
In many cases, you want to introduce a dependency on entries in other tables. For example, a trial is associated with a unique subject. This kind of relation is expressed with foreign keys.

What is a Foreign Key?
----------------------
A [foreign key](http://en.wikipedia.org/wiki/Foreign_key foreign key) is a referential constraint which precludes the existence of a tuple in one table (dependent table) without there existing the matching tuple in a another (referenced) table.

They are declared by including the line``-> <package>.<AnotherBaseRelvar>`` in matlab, and In Python, we indicate foreign keys by ``-> AnotherBaseRelvar`` in python. where ```package.AnotherBaseRelvar`` is the base *relvar* class name of the referenced table.

A foreign key can act as part of a primary key. For example in the ``Trials`` class, we define the foreign key ``subj.Subjects``. However, we also need another primary key to allow for several trials. If the primary keys were just the foreign key, we could only have one trial per subject.

Foreign keys create a dependency hierarchy which is reflected by the *entity relationship diagram*.

Adding a foreign key has the following effects. Consider the two Session and Scan Tables, where a scan is associated with a particular session.:

	* The primary key attributes of the referenced table are added to the declaration of the dependent table. For example, the primary key of `common.TpScan` in our example will become `(animal_id,tp_session,scan_idx)`.
	* It will no longer be possible to enter data into `common.TpScan` before entering entries into `common.TpSession` with identical values of `(animal_id,tp_session)`.
	* [Deleting data](2015-05-05-deletingdata) from `common.TpSession` will also delete all matching data from `common.TpScan`.
	* [Dropping table](post_url 2015-05-05-droppingtables) `common.TpSession` will also drop table `common.TpScan`.

To enforce referential integrity, DataJoint does not provide the ability to updates individual field values in an existing tuple: To change the value of a tuple, one must be delete it first and then insert it again, allowing the database to check and enforce the referential constraints.

Matlab Foreign Key Example
++++++++++++++++++++++++++

For example, table [`common.TpScan`](http://github.com/atlab/commons/blob/master/schemas/+common/TpScan.m) references table [`common.TpSession`](http://github.com/atlab/commons/blob/master/schemas/+common/TpSession.m).

.. code-block:: matlab

	%{
	common.TpScan (manual) # scanimage scan info
	->common.TpSession
	scan_idx : smallint # scanimage-generated sequential number
	-----
	surfz               : float   # (um) z-coord at pial surface
	depth=0             : int     # manual depth measurement
	laser_wavelength    : float # (nm)
	laser_power         : float # (mW) to brain
	cortical_area="V1"  : enum('other','unknown','V1','LM','AL','PM') # Location of scan
	scan_notes = ""     : varchar(4095)  #  free-notes
	scan_ts = CURRENT_TIMESTAMP : timestamp   # don't edit
	%}


Python Foreign Key Example
++++++++++++++++++++++++++

The equivalent definition in python would look like

.. code-block:: python

  import where_TpSession_lives

  schema = dj.schema('common', locals())

  @schema
  class TpScan(dj.Manual):

  	definition = """
  	# scanimage scan info

  	->where_TpSession_lives.TpSession
  	scan_idx : smallint # scanimage-generated sequential number

  	-----

  	surfz               : float   # (um) z-coord at pial surface
  	depth=0             : int     # manual depth measurement
  	laser_wavelength    : float # (nm)
  	laser_power         : float # (mW) to brain
  	cortical_area="V1"  : enum('other','unknown','V1','LM','AL','PM') # Location of scan
  	scan_notes = ""     : varchar(4095)  #  free-notes
  	scan_ts = CURRENT_TIMESTAMP : timestamp   # don't edit

  	"""

You can use whatever name the relation class has in the local context. For instance, you could import a class called `Animals` from another schema via `from my_other_schema import Animals as LabAnimals` and then refer to it in the table definition as `->LabAnimals`. This approach is quite powerful and gives the user a lot of freedom how to define and group schemata.

Note that the foreign key is specified by the *local name* of the other table. If `common.TpSession` had been imported via `from where_TpSession_lives import TpSession as peter`, you would define the foreign key as `->peter`.


Attributes
==========

The attributes below the line define regular subordinate attributes. They are specified via:

.. code-block:: matlab

	attribute_name  :  datatype   # comment
	attribute_name = default : datatype  # with a default value


the attribute name must start with a lowercase letter and contain only lowercase letters, digits, and underscores.

`datatype` is a MySQL datatype. Supported datatypes can be found [here](/datatypes/).

When the default value is provided, tuples may be inserted without specifying that attribute and the default value will be inserted.  String values and enum values must be enclosed in double quotes `""`.

When the special value of `null` is specified as the default, the the attribute is specified as _nullable_. If a tuple is inserted without specifying that field, the value will be missing.  For string attributes, instead of specifying null, consider defaulting to `""`.  An empty string and null are two different things but empty strings are usually easier to work with. Primary key fields cannot be nullable.

The special value `CURRENT_TIMESTAMP` can be used as the default of `timestamp` fields, which is commonly used to timestamp the data.

secondary indices
=================

You can add secondary (unique) indices to the end of your table declaration:

.. code-block:: matlab

	+++
	INDEX(attribute1)
	INDEX(attribute1,attribute2,attribute3)
	UNIQUE INDEX(attribute1)
	UNIQUE INDEX(attribute1,attribute2,attribute3)



Dropping tables
===============

To *drop a table* means to remove the table from the database and all its dependent tables recursively with all their contents without removing its MATLAB class definition files. If there are any data in the tables to be dropped, DataJoint will report the total number of tuples and will prompt for an additional confirmation before dropping.

Dropping tables is often necessary when the table definition needs to be significantly modified or when the table is no longer necessary. Remember that the table will be created again automatically upon the next instantiation of its base relvar class, so if you want to ensure that the table never comes back, delete its class definition file as well.


Matlab dropping
---------------

All [base relvars](2015-05-05-baserelvars) have the constant property **table** of type **dj.Table**, which provides access to the table definition.

For example, to drop the table of the class `tp.Segmentation`

.. code-block:: matlab

	>> tp.Segmentation.table.drop


See also

.. code-block:: matlab

	>> help dj.Table/drop



Python dropping
---------------

In Python, the equivalent call would be


.. code-block:: python

	tp.Segmentation.drop()


This call will drop the table and all tables that reference it, recursively. If you only want to drop one table, without dropping the referencing tables (not recommended!), you can use

.. code-block:: python

	tp.Segmentation.drop_quick()


Note that the classes that refer to the table can still exist after dropping a table. If you try to create a new instance from it, an error will be raised.


Modifying Tables
================

Once a table has been declared and populated, it is still possible to change the structure of the table and its foreign keys. These changes are accomplished by the following methods:

Matlab
------

.. code-block:: matlab

  dj.Table/addAttribute
  dj.Table/alterAttribute
  dj.Table/dropAttribute

  dj.Table/addForeignKey
  dj.Table/dropForeignKey

  dj.Table/addIndex
  dj.Table/dropIndex

  dj.Table/setTableComment


For example, change the definition of the existing field `nframes` in table `tp.Align`


.. code-block:: matlab

	tp.Align.table.alterAttribute('nframes', 'nframes: smallint  # total number of frames')


Each of these functions will request user confirmation before automatically updating the table declaration in the class file.

Python
------

In Python these functions are not provided at the moment,  but we are working on it ...
