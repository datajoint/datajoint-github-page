---
layout: post
title: 'Projection'
tags: ['tutorial','matlab','python','help']
summary: 'Projection operator'
---

DataJoint's projection operator `pro` derives a new relvar that has the same number of tuples as the input relvar but with a modified set of attributes (columns).

A single call to `pro` can perform any combination of the following [relational operators](http://en.wikipedia.org/wiki/Relational_algebra):

* [project](http://en.wikipedia.org/wiki/Projection_%28relational_algebra%29)
* [rename](http://en.wikipedia.org/wiki/Rename_%28relational_algebra%29)
* [extend](http://en.wikipedia.org/wiki/Relational\_algebra#The_extend_operation)
* [aggregate](http://en.wikipedia.org/wiki/Relational\_algebra#Aggregation)

Let's look at each of these separately.

## project
The project operator selects a subset of attributes from the original relation. The primary key is always included and cannot be projected out. Thus if no fields are specified, then the result will contain the primary key of the input relation.


## rename 
Attributes of a relation can be renamed by adding arguments of the form `'old_name->new_name'`. Renamed attributes play an important role in conjunction with the [[join]] operator.

For example, in Matlab

```
c = tp.Cells;
c1 = c.pro('cell_id->c1','cell_x->x1','cell_y->y1');
c2 = c.pro('cell_id->c2','cell_x->x2','cell_y->y2');
pairs = c1*c2;
```

Here the resulting relation `pairs` will contain pairs of cells that have all primary key attributes matching except for `cell_id`, i.e. pairs of cells from each site. Projection removes all unmentioned attributes except for the primary key.  Thus `pairs` will have all pairs of cells and their coordinates in one relation. 

## extend
New attributes can be computed from existing attributes by adding arguments of the form `'expression->new_name'`.

For example, in Matlab

```
deepScans = pro(common.TpScans,'*','z−surfz −> depth') & `depth > 250'
```

adds the new field `depth` to `common.TpScans`, which is computed as the difference of fields `z` and `surfz`. The relation is then restricted to depths greater than 250 to select the deep scans.

## aggregate
Another form of the projection operator takes two relations as input and allows adding summary computations on tuples in the second relation as attributes of the first relation. In this case, `pro` takes two relvars as arguments and the expression can include the [aggregation functions](http://dev.mysql.com/doc/refman/5.6/en/group-by-functions.html) `count()`, `count(distinct ...)`, `avg()`, `max()`, `min()`, `sum()`, `variance()`, and `std()`.

```
P=pro(R,Q,'summary_expression−>attr1',..)
```

The resulting relation P will have the same primary key as R and a new attribute attr1 which contains a summary computation on matching groups of tuples in Q.

For example, in Matlab

```
P=pro(P,Q,'*','count(*)−>n')
```

keeps all the old attributes of P and adds the new computed attribute n containing the number of matching tuples in Q for each tuple in P. 

Another example, 

```
P=pro(R,Q,'avg(pvalue<0.05)−>frac_significant')
```

creates the relation P containing the fraction of tuples in Q whose attribute `pvalue` is less than 0.05 for every matching tuple in R.

Another example

```
richSessions = pro(psy.Sessions, psy.Trials, 'count(distinct cond_id)->nconds') & 'nconds > 30'
```

produces the selection of psychophysics sessions in which the number of unique condition ids across all trials exceeds 30.

See also

```
>> help dj.GeneralRelvar/pro
```