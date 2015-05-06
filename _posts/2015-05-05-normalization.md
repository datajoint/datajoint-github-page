---
layout: post
title: 'Normalization'
tags: ['tutorial','matlab','python','help']
summary: 'How to normalize tables'
---

This section reviews a few basic rules for relational data design.


## Atomize attributes

A new programmer may be tempted to declare a few  [datatype blob](/datatypes/) attributes and stuff complex MATLAB structures into each attribute value.  To allow precise and efficient queries, structured values must be broken up into separate columns possibly across multiple tables.  Some large and complex  objects such as images or traces that are always manipulated atomically, should be kept together in a single field.  In summary, if you plan to search by the value of an attribute or retrieve it separately from other attributes, it needs its own column.

## Normalize tables

Before learning the relational model, most programmers tend to lump too many things in one table similar to working with spreadsheets.  Relations are not spreadsheets.  [Database normalization](http://en.wikipedia.org/wiki/Database_normalization) is the systematic process of dividing tables into separate simpler tables until they meet formal sets of criteria known as [Normal Forms](http://en.wikipedia.org/wiki/Database_normalization). Properly normalized tables store all information exactly once and relate it precisely to other information, precluding many anomalies arising in data manipulations.

The normal forms have been humorously summed up by this solemn oath:  "Every attribute in a table must relate a fact about the primary key, the whole key, and nothing but the key --- so help me [Codd](http://en.wikipedia.org/wiki/Edgar_F._Codd)".

 If some attributes are not directly associated with the entire [primary key]({% post_url 2015-05-05-primarykeys %}), they must reside in a different table with a different primary key.  

Here are a few common signs indicating that a table may need to be split:

* The same data needs to be entered or updated in more than one place, possibly in different forms.  This indicates that the duplicated data must be moved to their separate table and referred to by other tables. 
* Some attributes do not apply to all tuples in a table.  Your table appears to contain different kinds of facts, which is not allowed in a normalized design. You will need to split this table into two with different sets of attributes. 

DataJoint by design encourages well normalized data. As you design your first tables, you will gain intuition as to which columns belong together in the same table. If you are more curious, for extra credit, review the first three normal forms applicable to DataJoint: 

* [first normal form](http://en.wikipedia.org/wiki/First_normal_form)
* [second normal form](http://en.wikipedia.org/wiki/Second_normal_form)
* [third normal form](http://en.wikipedia.org/wiki/Third_normal_form)

Table design, including the choice of the [primary keys]({% post_url 2015-05-05-primarykeys %}) and the specification of [foreign keys]({% post_url 2015-05-05-foreignkeys %}), effectively communicates and enforces the rules and the structure of your data.  As rules change, you can change the structure of your schema or design a new schema altogether and migrate your old data there.