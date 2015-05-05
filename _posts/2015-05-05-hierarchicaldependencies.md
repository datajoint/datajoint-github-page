---
layout: post
title: 'Hierarchical dependencies'
tags: ['tutorial','matlab','python','help']
summary: 'Hierarchical dependencies between data entries.'
---

In his [original paper](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.110.1845&rep=rep1&type=pdf) introducing the relational data model, [E.F.Codd](http://en.wikipedia.org/wiki/Edgar_F._Codd) proposed a simple scheme for [normalizing](http://code.google.com/p/datajoint/wiki/DataDefinition) hierarchical data into the relational model: 
> Starting with the relation at the top of the tree, take its primary key and expand each of the immediately subordinate relations by inserting this primary key domain or domain combination. The primary key of each expanded relation consists of the primary key before expansion augmented by the primary key copied down from the parent relation. Now, strike out from the parent relation all nonsimple domains, remove the top node of the tree, and repeat the same sequence of operations on each remaining subtree.

DataJoint naturally encourages this approach to the normalization of hierarchical dependencies. When a foreign key is added to the table declaration, the primary key fields of the referenced table are copied with the same names.  If the reference is added above the divider `---`, the inherited primary key fields are added to the primary key, in which case they will be propagated further down the hierarchy. This allows relating data across the hierarchy very easily: the primary key value from one table will match precisely all the matching data from any other table in the hierarchy without traversing data paths.