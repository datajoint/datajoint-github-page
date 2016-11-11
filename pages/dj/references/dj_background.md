## Background

Invented by IBM researcher [Edgar F. Codd](http://en.wikipedia.org/wiki/Edgar_F._Codd) in 1969, (E. F. Codd. "A relational model of data for large shared data banks." _Communications of the ACM_, 13(*6*):387, 1970) the [relational model](http://en.wikipedia.org/wiki/Relational_model) for databases steadily replaced the earlier hierarchical and network models and has become the de facto standard for mainstream databases today, supporting banking transactions, airfare bookings, and data-intensive websites such as Facebook, Google, Wikipedia, and YouTube, to pick but a few examples.  Modern relational database management systems  execute fast, precise, and flexible data queries and preclude inconsistencies arising from simultaneous or interrupted manipulations by multiple users. Interactions with a relational database are performed in a query language such as [SQL](http://en.wikipedia.org/wiki/SQL SQL). 

## Basic concepts
The following is a very brief informal introduction to  the relational data model as implemented by DataJoint.

### tuples
A _tuple_ is a set of attribute name/value pairs. For example, the tuple

     mouse_id   measure_date   weight
       1001      2010-10-10     21.5

in a given relation may represent a real-world fact such as "On Oct. 10, 2010, mouse #1001 weighed 21.5 grams.''  An attribute name is more than just a name: it implies a particular _datatype_ (domain) and a unique _role_ in the tuple and in the external world.  Thus attribute names must be unique in a tuple and their order in the tuple is not significant.  

The closest equivalent of a tuple in Matlab is a structure.

We will use the terms _attribute_ and _field_ interchangeably.

### relations
A relation is a set of tuples that share the same set of attribute names. No duplicate tuples can exist in a relation.  The ordering of tuples in a relation is not significant.

A _base relation_ is the contents of a _table_ in the database. Operators of relational algebra produce new _derived relations_ from other relations.

### relvars
A relation is a constant object. In contrast, a relvar (relation variable) is a variable that holds a relation.

Starting with a _base relvar_ expressing the complete contents of a given _table_ in the database, we can transform them into _derived relations_ by applying _relational operators_ until they contain only all the necessary information, and then retrieve their values into the MATLAB workspace.  We will use the term _table_ and _base relvar_ interchangeably.

### matching tuples
The key concept at the foundation of data manipulations in the relational model is _tuple matching_.  Two tuples match if their identically named attributes contain equal values.  

Thus one tuple may be used to address a group of  other matching tuples in a relation (e.g. see the restrict, semijoin, antijoin, and union operators in [[restriction]]). Two tuples may be merged into one if they match, but not otherwise. The _join_ of two relations is the set of all possible merged pairs from the two original relations.

### primary keys
In DataJoint, each relation has one _primary key_ comprising a subset of its attributes that are designated to uniquely identify any tuple in the relation. No two tuples in the same relation can have the same combination of values in their primary key fields.  To uniquely identify a tuple in the relation, one must provide the values of the primary key fields as a matching tuple.

Learn more in [[primary keys]].

### foreign keys
A base relation may have one or more _foreign keys_, i.e. a subset of its attributes that correspond to the primary key of another, _referenced_ base relation. The database will enforce _referential constraints_: a base relation cannot contain a tuple whose foreign key value does not match the primary key value of a tuple in the referenced relation.

Learn more in [[foreign keys]]
