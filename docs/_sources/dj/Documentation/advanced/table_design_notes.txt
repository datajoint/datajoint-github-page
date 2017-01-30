Structuring Data
****************

Hierarchical dependencies
=========================

.. _`original paper`: http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.110.1845&rep=rep1&type=pdf
.. _`E.F.Codd`: http://en.wikipedia.org/wiki/Edgar_F._Codd

.. _`normalizing`: http://code.google.com/p/datajoint/wiki/DataDefinition


In his `original paper`_ introducing the relational data model, `E.F.Codd`_ proposed a simple scheme for `normalizing`_ hierarchical data into the relational model:

> Starting with the relation at the top of the tree, take its primary key and expand each of the immediately subordinate relations by inserting this primary key domain or domain combination. The primary key of each expanded relation consists of the primary key before expansion augmented by the primary key copied down from the parent relation. Now, strike out from the parent relation all nonsimple domains, remove the top node of the tree, and repeat the same sequence of operations on each remaining subtree.

DataJoint naturally encourages this approach to the normalization of hierarchical dependencies. When a foreign key is added to the table declaration, the primary key fields of the referenced table are copied with the same names.  If the reference is added above the divider ``---``, the inherited primary key fields are added to the primary key, in which case they will be propagated further down the hierarchy. This allows relating data across the hierarchy very easily: the primary key value from one table will match precisely all the matching data from any other table in the hierarchy without traversing data paths.

normalization
=============

This section reviews a few basic rules for relational data design.


Atomize attributes
------------------

A new programmer may be tempted to declare a few  [datatype blob](/datatypes/) attributes and stuff complex MATLAB structures into each attribute value.  To allow precise and efficient queries, structured values must be broken up into separate columns possibly across multiple tables.  Some large and complex  objects such as images or traces that are always manipulated atomically, should be kept together in a single field.  In summary, if you plan to search by the value of an attribute or retrieve it separately from other attributes, it needs its own column.

Normalize tables
------------------


.. _`Database normalization`: http://en.wikipedia.org/wiki/Database_normalization
.. _`Normal Forms`: http://en.wikipedia.org/wiki/Database_normalization
.. _`normalizing`: http://code.google.com/p/datajoint/wiki/DataDefinition


Before learning the relational model, most programmers tend to lump too many things in one table similar to working with spreadsheets.  Relations are not spreadsheets.  `Database normalization`_ is the systematic process of dividing tables into separate simpler tables until they meet formal sets of criteria known as `Normal Forms`_. Properly normalized tables store all information exactly once and relate it precisely to other information, precluding many anomalies arising in data manipulations.

The normal forms have been humorously summed up by this solemn oath:  "Every attribute in a table must relate a fact about the primary key, the whole key, and nothing but the key --- so help me `Codd <http://en.wikipedia.org/wiki/Edgar_F._Codd>`_".

 If some attributes are not directly associated with the entire [primary key](/2015-05-05-primarykeys), they must reside in a different table with a different primary key.

Here are a few common signs indicating that a table may need to be split:

* The same data needs to be entered or updated in more than one place, possibly in different forms.  This indicates that the duplicated data must be moved to their separate table and referred to by other tables.
* Some attributes do not apply to all tuples in a table.  Your table appears to contain different kinds of facts, which is not allowed in a normalized design. You will need to split this table into two with different sets of attributes.

DataJoint by design encourages well normalized data. As you design your first tables, you will gain intuition as to which columns belong together in the same table. If you are more curious, for extra credit, review the first three normal forms applicable to DataJoint:

* `first normal form <http://en.wikipedia.org/wiki/First_normal_form>`_
* `second normal form <http://en.wikipedia.org/wiki/Second_normal_form>`_
* `third normal form <http://en.wikipedia.org/wiki/Third_normal_form>`_

Table design, including the choice of the primary keys and the specification of foreign keys, effectively communicates and enforces the rules and the structure of your data.  As rules change, you can change the structure of your schema or design a new schema altogether and migrate your old data there.
