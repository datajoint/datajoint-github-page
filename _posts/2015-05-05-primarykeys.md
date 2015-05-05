---
layout: post
title: 'Primary keys'
tags: ['tutorial','matlab','python','help']
---

### What's a primary key
Each table must have exactly one [primary key](http://en.wikipedia.org/wiki/Primary_key): a subset of its attributes that uniquely identify each tuple in the table.  The database uses the primary key to prevent duplicate entries, to relate data across tables, and to accelerate data queries. The choice of the primary key will determine how you identify tuples. Therefore, make the primary key **short**, **expressive**, and **persistent**.
For example, mice in our lab are assigned unique IDs. The mouse ID number of type `smallint` can serve as the primary key for the table `Mice`.

### Datatypes in primary keys
All integer types, dates, and timestamps make good primary key attributes. String (`char` and `varchar`) are less common because they can be quite long and because they may have invisible trailing spaces. Floating-point numbers should be avoided because rounding errors may lead to logical errors. Enums are okay as long as they do not need to be modified later when [[foreign keys]] are already created referencing the table. Finally, dataJoint does not support blob types in primary keys.

The primary key may be composite, i.e. comprising several attributes. In DataJoint, hierarchical designs often produce tables whose primary keys comprise many attributes.

### Natural primary keys
A primary key comprising real-world attributes is a [natural primary key](http://en.wikipedia.org/wiki/Natural_key). Natural primary keys are a good choice when such real-world attributes are already properly assigned and their permanence is ensured.  If no convenient natural key exists, you may choose to introduce a [surrogate primary key](http://en.wikipedia.org/wiki/Surrogate_key), i.e. an artificial attribute whose purpose is to uniquely identify tuples in a table.  An institutional process must ensure the uniqueness and permanence of a surrogate key. For example, the U.S. government assigns every worker a surrogate attribute, the social security number, but the government must go to great lengths to ensure that this primary key is assigned exactly once by checking against other less convenient candidate keys (i.e. the combination of name, parents' names, date of birth, place of birth, etc.)  Just like the SSN, well managed surrogate keys tend to get institutionalized and, overtime, become natural.