About DataJoint
***************


.. toctree::
   :titlesonly:

   About/history
   About/citing
   About/license
   About/faq


This page is written to help you decide if DataJoint is the right tool for your application.

What is DataJoint?
==================

.. _Python3: https://github.com/datajoint/datajoint-python
.. _MATLAB: http://datajoint.github.io/datajoint-matlab/

* DataJoint for MATLAB_ and Python3_ is a high-level programming interface for MySQL databases to support data processing chains in science labs.
* DataJoint is built on the foundation of the relational data model and prescribes a consistent method for organizing, populating, and querying data.


Designed for busy scientists and collaborative data sharing
-----------------------------------------------------------

The amount of data produced by scientific labs has been steadily increasing. Ensuring long-term accessibilty and reproducibility of results with data aquired by several people, sometimes in several different labs, has become a challenge. In many labs, the default data management scheme comprises the OS file system with clever naming conventions with additional meta data stored in CSV, XML, .MAT files. This works fine for dead storage, but makes more interesting queries difficult, provides no easy way to maintain data dependencies, and becomes problematic when data is shared across labs.

For example, imagine that you want to compute a summary of all experiments that have been done in your lab during the last 7 years using a particular experimental setting. With a data management scheme that relies on naming of folders you are likely to spend a good amount of time on crawling the different directories and extracting the relevant datasets. Since the naming scheme is never enforced, single persons can (and probably will) violate it. Unfortunately, every deviation from it will require you to either include extra cases in your script or to fix the naming manually. The latter case will almost certainly break the analysis code of some other person in your lab.

In DataJoint, the data for the above task is just one line of code. DataJoint provides a flexible, light-weight, yet capable system that allows you to organize your data and quickly access it. Even better, it is not hard to learn and use.

DataJoint is based on the relational data model and object-relational mapping
-----------------------------------------------------------------------------

We know what you are thinking: *There you promised us something that is easy to learn and use, and here the next heading is only comprehensible by people with a computer science major*. However, the essence of these two central principles of DataJoint is natural and easy to understand:

Data is organized in tables (relational data model):
----------------------------------------------------
.. _`Edgar F. Codd`: http://en.wikipedia.org/wiki/Edgar_F._Codd
.. _`relational model`: http://en.wikipedia.org/wiki/Relational_model
.. _`SQL`: http://en.wikipedia.org/wiki/SQL

Organizing data in tables is quite natural. Everyone who already worked with Excel or .csv files has experience with it. The only crucial difference to a relational data model is that each row in a table has to be unique. Those kind of tables are traditionally called *relations*. The relational data model was invented by IBM researcher `Edgar F. Codd`_ in 1969, (E. F. Codd. "A relational model of data for large shared data banks." Communications of the ACM, 13(*6*):387, 1970) the `relational model`_ for databases steadily replaced the earlier hierarchical and network models and has become the de facto standard for mainstream databases today, supporting banking transactions, airfare bookings, and data-intensive websites such as Facebook, Google, Wikipedia, and YouTube, to pick but a few examples.  Modern relational database management systems  execute fast, precise, and flexible data queries and preclude inconsistencies arising from simultaneous or interrupted manipulations by multiple users. Interactions with a relational database are performed in a query language such as SQL_.


Database tables are hidden behind Matlab/Python objects (object-relational mapping)

DataJoint stores data in regular MySQL tables. Why MySQL? Because it is optimized for storing and retrieving data, and it ensures a wide accessibility of the data. Any other language that has a MySQL API can be adapted to exchange data with DataJoint applications.

.. _`object-relational mapping`: http://en.wikipedia.org/wiki/Object-relational_mapping


Unfortunately, SQL queries are typically cumbersome. SQL requires significant training to be used effectively. DataJoint makes it easy for you to deal with the tables by hiding them behind Matlab/Python objects. All data definition and manipulation tasks are performed from Matlab/Python using Matlab/Python language constructs. DataJoint associates each table in the database with a Matlab/Python class. Users manipulate data by invoking these classes that encapsulate the corresponding tables. This is called `object-relational mapping`_.

Creating tables with DataJoint is simple
----------------------------------------
DataJoint provides its own syntax for creating tables directly from Matlab/Python code. Tables are created automatically upon the first invocation of the table in application code.

.. _`psy.Trial`: https://github.com/dimitri-yatsenko/dj-schema-psy/blob/master/Trial.m

For example, a table `psy.Trial`_ defining visual stimulus trials would be defined in Matlab simply as a comment in a file called *Trial.m*

.. code-block:: matlab

  %{
  psy.Trial (manual) # visual stimulus trial
  -> psy.Session
  trial_idx       : int         # trial index within sessions
  ---
  -> psy.Condition
  flip_times                  : mediumblob          # (s) row array of flip times
  last_flip_count             : int unsigned        # the last flip number in this trial
  trial_ts=CURRENT_TIMESTAMP  : timestamp           # automatic
  %}


In python, the corresponding definition looks like

.. code-block:: python

  import datajoint as dj
  schema = dj.schema('mydatabase', locals())

    @schema
    class Trial(dj.Manual):
    	definition = """
    	# visual stimulus trial

        -> psy.Session
    	trial_idx       : int   # trial index within sessions
    	---
    	-> psy.Condition
    	flip_times                  : mediumblob   # (s) row array of flip times
    	last_flip_count             : int unsigned # the last flip number in this trial
    	trial_ts=CURRENT_TIMESTAMP  : timestamp    # automatic
    	"""



The corresponding MySQL command that DataJoint generates automatically upon the first use of the class is

.. code-block:: sql

  CREATE TABLE `trial` (
  `animal_id` int(11) NOT NULL COMMENT 'id (internal to database)',
  `psy_id` smallint(5) unsigned NOT NULL COMMENT 'unique psy session number',
  `trial_idx` int(11) NOT NULL COMMENT 'trial index within sessions',
  `cond_idx` smallint(5) unsigned NOT NULL,
  `flip_times` mediumblob NOT NULL COMMENT '(s) row array of flip times',
  `last_flip_count` int(10) unsigned NOT NULL COMMENT 'the last flip number in this trial',
  `trial_ts` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'automatic',
  PRIMARY KEY (`animal_id`,`psy_id`,`trial_idx`),
  KEY `animal_id` (`animal_id`,`psy_id`,`cond_idx`),
  CONSTRAINT `trial_ibfk_1` FOREIGN KEY (`animal_id`, `psy_id`)
      REFERENCES `session` (`animal_id`, `psy_id`) ON UPDATE CASCADE,
  CONSTRAINT `trial_ibfk_2` FOREIGN KEY (`animal_id`, `psy_id`, `cond_idx`)
      REFERENCES `condition` (`animal_id`, `psy_id`, `cond_idx`) ON UPDATE CASCADE
  ) ENGINE=InnoDB DEFAULT CHARSET=latin1 COMMENT='visual stimulus trial'


While you probably have a pretty good idea about what is going on in the Python or the Matlab code, it is a lot harder to figure that out for the MySQL command. Luckily, you will never have to deal with it.

Simple code sharing
-------------------
Since table definitions are included in simple Matlab/Python files, you only need to share these files to replicate the same functionality somewhere else.

Automated computations with referential integrity
-------------------------------------------------
DataJoint implements a standard process for populating computed data. This process uses referential constraints to enforce all data dependencies. This processes also uses transactional processing to ensure  that interrupted computations do not result in partially computed invalid results.

Support for distributed/parallel processing
-------------------------------------------
DataJoint provides a job reservations process to enable distributed processing on multiple processors or computers without conflict.

What DataJoint is not
=====================

DataJoint is not a general-purpose interface
--------------------------------------------
DataJoint is designed for the sole purpose of providing a robust and intuitive data model for scientific data processing chains. As such it does not reproduce all the features and capabilities of SQL. To achieve its strict adherence to the relational data model, its expressive power and simplicity, DataJoint imposes some limitations and conventions. This is done intentionally to avoid dangerous usage of SQL and to make the data model logically sound.

Here are some examples of such conventions and limitations:

* DataJoint does not allow updating an individual attribute value in a given tuple (SQL's UPDATE command): all data manipulations are done by inserting or deleting whole tuples. This is done for good reason since referential constraints (foreign keys) only enforce data dependencies between tuples but not between individual attributes.

* DataJoint limits some operators to enforce clarity. For example, its projection operator does not allow projecting out any of the primary key attributes. This ensures that the result of the projection operator has the same cardinality as the original relation (the same number of rows). If the user really intends to produce a relation with a different primary key, she must explicitly declare such a relation in the form of a [base relvar](2015/05/05/baserelvars/). Again, this is not a real limitation but a specific prescription of how things should be done in a uniform manner.

* In DataJoint, all foreign keys between tables are formed between identically named fields. This convention allows easy specification of functional dependencies and easy relational join operators. It also allows to replace the many forms of the join operator in other language with a single natural join operator. In a large schema, this convention may lead to long composite primary keys in tables that are low in the dependency hierarchy, but MySQL handles these with ease.  This convention is particularly important in DataJoint because it allows tables across the database or multiple databases to be logically linked without having to follow the path of intermediate dependencies.
