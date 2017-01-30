Representing Table Relationships
********************************

.. toctree::
   :maxdepth: 6
   :hidden:

   self

What’s a base relvar?
=====================

.. _`latest release`: https://github.com/datajoint/datajoint-matlab/releases

A base relvar object represents the relation containing the entire contents of its table. Other relations can be derived from base relvars using relational operators. Note that the relvar objects do not themselves contain any data. They only form the specific query that can be used to fetch data into the current workspace.


A DataJoint *schema* is the pairing of a MySQL database with a Matlab/Python package.

A *base relvar* is a MATLAB/Python class in a DataJoint schema associated with a table in the database.

Users first create base relvars, which in turn automatically create their tables. Thus a base relvar provides a full interface to its table.

The name of the created table will be similar to the name of the class with a prefix indicating the table tier. For example, the computed base relvar `tp.OrientationTuning` will create the database table `__orientation_tuning`, where the double-underscore prefix indicates that this is a computed table However, DataJoint users never need to address tables by their internal names.


Matlab Examples
===============

The `display` method shows few tuples from the table and the total count of tuples.  For example, simply typing the class name of `common.Animal` from the *common* schema will display

.. code-block:: matlab

  >> common.Animal

  self =

    common.Animal handle
    Package: common

    Properties:
             table: [1x1 dj.Table]
            schema: [1x1 dj.Schema]
            header: [6x1 struct]
               sql: '`common`.`animal`'
        primaryKey: {'animal_id'}
      nonKeyFields: {'real_id'  'date_of_birth'  'sex'  'animal_notes'  'animal_ts'}
      restrictions: {}

    Methods, Events, Superclasses


       animal_id       real_id  date_of_birt           sex  animal_notes     animal_ts
               0             0    2001-01-01       unknown  testing dumm  2012-04-17 0
               4   10575:33922    0000-00-00       unknown  Keith's GCaM  2012-07-11 1
             408           408    2011-04-11             F        C57BL6  2012-03-23 0
             436           325    2012-02-15             M                2012-03-24 1
             444           444    2012-02-21             F        C57BL6  2012-03-21 1
             445           445    2012-02-21             F        C57BL6  2012-03-22 0
             467           467    2012-02-24             M        C57BL6  2012-03-27 1
             476           476    2012-03-02             M        C57BL6  2012-03-29 1
             477           477    2012-03-02             M                2012-04-05 1
             480           480    2012-03-11             M        C57BL6  2012-04-16 1
             481           481    2012-03-11             M        C57BL6  2012-04-12 1
             482           482    2012-03-11             M                2012-04-17 1
           .....         .....         .....         .....         .....         .....
  45 tuples (0.0382 s)



Defining Schema
---------------

To create a new base relvar interactively, use the **dj.new** command:

.. code-block:: matlab

  >> dj.new

You will be prompted to enter the class name, its tier, and its foreign keys. The class name must be in format `package.ClassName`. The `ClassName` must start with a capital letter and only include alphanumerical characters.

Then **dj.new** will automatically create a new .m file with the complete definition of the class. For example, if we specified the class name `tp.Test` with tier `manual`, the class definition file will be

.. code-block:: matlab

  %{
  tp.Test (manual) # my newest table

  -----

  %}

  classdef Test < dj.Relvar
  end



The first percent-brace comment block that starts with `%{` and ends with `%}` has special meaning in DataJoint. It contains the [table declaration](/2015/05/05/tabledeclaration/).  Upon the first invocation of an object of this class, if the table is not yet created in the database, DataJoint will automatically create the table based on this declaration.
### Python

Python does not have a similar script. If you think that such a script is urgently needed, please fork [datajoint-python](https://github.com/datajoint/datajoint-python) on github, create such a function, and send us a pull request. If you think that coding such a function exceeds your coding abilities, you can also raise an issue in the [issue tracker](https://github.com/datajoint/datajoint-python/issues) on github.
## interactive creation


## display Schema
### Matlab

### Python
In python you can simply call `print(animal)` if `animal` is an instance of the `common.Animal` schema. This will print a similar output to the prompt.


## Primary keys

### Matlab

### Python



## Foreign keys

### Matlab

### Python
