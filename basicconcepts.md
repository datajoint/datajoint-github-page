---
layout: page
title: Basic database concepts
---
{% include JB/setup %}

The following is a very brief informal introduction to  the relational data model as implemented by DataJoint.

### Tuples
A _tuple_ is a set of attribute name/value pairs. For example, the tuple

     mouse_id   measure_date   weight 
       1001      2010-10-10     21.5

in a given relation (table) may represent a real-world fact such as "On Oct. 10, 2010, mouse #1001 weighed 21.5 grams.''  An attribute name is more than just a name: it implies a particular _datatype_ (domain) and a unique _role_ in the tuple and in the external world.  Thus attribute names must be unique in a tuple and their order in the tuple is not significant.  

The closest equivalent of a tuple in Matlab is a structure. The closest data structure in Python is a named tuple.

We will use the terms _attribute_ and _field_ interchangeably.

### Relations
A relation is a set of tuples that share the same set of attribute names. No duplicate tuples can exist in a relation.  The ordering of tuples in a relation is not significant.

A _base relation_ is the contents of a _table_ in the database. Operators of relational algebra produce new _derived relations_ from other relations.

### Relvars
A relation is a constant object. In contrast, a relvar (relation variable) is a variable that holds a relation. 

Starting with a _base relvar_ expressing the complete contents of a given _table_ in the database, we can transform them into _derived relations_ by applying _relational operators_ until they contain only all the necessary information, and then retrieve their values into the Matlab/Python workspace.  We will use the term _table_ and _base relvar_ interchangeably. 

### Matching tuples
The key concept at the foundation of data manipulations in the relational model is _tuple matching_.  Two tuples match if their identically named attributes contain equal values.  

Thus one tuple may be used to address a group of  other matching tuples in a relation (e.g. see the restrict, semijoin, antijoin, and union operators in [[restriction]]). Two tuples may be merged into one if they match, but not otherwise. The _join_ of two relations is the set of all possible merged pairs from the two original relations.

### Primary keys
In DataJoint, each relation has one _primary key_ comprising a subset of its attributes that are designated to uniquely identify any tuple in the relation. No two tuples in the same relation can have the same combination of values in their primary key fields.  To uniquely identify a tuple in the relation, one must provide the values of the primary key fields as a matching tuple.

### Foreign keys
A base relation may have one or more _foreign keys_, i.e. a subset of its attributes that correspond to the primary key of another, _referenced_ base relation. The database will enforce _referential constraints_: a base relation cannot contain a tuple whose foreign key value does not match the primary key value of a tuple in the referenced relation. 
