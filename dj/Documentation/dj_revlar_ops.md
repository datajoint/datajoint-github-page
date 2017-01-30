---
title: Relvar Operations
permalink: dj_relvar_ops.html
sidebar: dj_sidebar
tags: [special_layouts]
keywords: join, merge, filter
last_updated: November 1, 2016
summary: ""

folder: dj/documentation
---




## Join
The [natural join](http://en.wikipedia.org/wiki/Relational_algebra#Natural_join_.28.E2.8B.88.29) operator between two relvars is implemented using the times operator `*`.

```
A*B
```

contains all fields in both `A` and `B`. The tuples of `A*B` contain the Cartesian product (all possible pairs) of tuples in `A` and `B` but keeps only those that match. Matching tuples are those in which identically named attributes have equal values.

In DataJoint schemas, primary key attributes of related tables share attribute names. Thus in most cases, joins are performed on primary keys.

A powerful way to control the result of the join operator is by renaming the attributes of one or both arguments.

Any attribute that is in the primary key of either `A` or `B` is included in the primary key of `A*B`.

For example
```
cells = tp.Cells * common.Session
```
creates the relation `cells` with the attributes of both `common.Session` and `tp.Cells` in matching tuples.




## Project

DataJoint's projection operator `pro` (or `project` in Python) derives a new relvar that has the same number of tuples as the input relvar but with a modified set of attributes (columns).

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



### rename

### extend

### aggregate



## Restriction

[Relational restriction](http://en.wikipedia.org/wiki/Selection_%28relational_algebra%29) selects the subset of tuples that meet a given condition.

In DataJoint, relational restriction is implemented using the operators `&` and `-`.

The following expression assigns to `rel` all the tuples from `original` that match condition `cond`.

```
rel = original & cond
```

The following expression assigns to `rel` all the tuples from `original` that do not match condition `cond`.

```
rel = original - cond
```

The condition `cond` can be another relvar, a structure array, or string containing a condition.


## Restriction by another relvar: _semjoin_ and _antijoin_

Recall that two tuples _match_ when their identically named attributes contain equal values.  When `cond` is another relvar, the result of `original & cond` will contain all tuples for which there exist a matching tuple in `cond`.

For example, referring to schema `common` and schema `psy`.

```
rel = (common.Animal & psy.Session) - common.TpSession
```
the resulting relation `rel` will contain all animals that have participated in at least one psychophysics session but have not had any two-photon imaging performed on them.

If `cond` does not have any attributes with the same names as in `original`, then `rel` will be equal to `original`.

In formal [relational algebra](http://en.wikipedia.org/wiki/Relational_algebra), restrictions by another relation are known as [semijoin](http://en.wikipedia.org/wiki/Relational_algebra#Semijoin_.28.E2.8B.89.29.28.E2.8B.8A.29) (`&`) and [antijoin](http://en.wikipedia.org/wiki/Relational_algebra#Antijoin_.28.E2.96.B7.29) (`-`).

## Restriction by a structure array
Relations can be represented in MATLAB by structure arrays, and dicts of a list of dicts in Python.  Restriction by a data structure has the same meaning as restriction by another relvar.  

For example, in Matlab,

```
rel = common.TpSession & struct('animal_id', 14526)
```

returns all two-photon sessions for animal 14526.

The corresponding Python call would be

```
rel = common.TpSession & dict(animal_id=14526)
```

Restriction by a structure plays an important role for addressing tuples or for iterating over tuples. For example, to review groups of tuples in `common.TpScan' for each `common.TpSession`, the following code could be used:

{% highlight matlab %}

% iterate through two-photon scans
for key = fetch(common.TpSession)'   % primary key values of two-photon sessions
    rel = common.TpScan & key;
    % rel now represents all scans for the session identified by key
    ... do something with it here
end
{% endhighlight %}

The corresponding Python code would be

{% highlight python %}

# iterate through two-photon scans
for key in common.TpSession:   # primary key values of two-photon sessions
    rel = common.TpScan & key
    # rel now represents all scans for the session identified by key
    # ... do something with it here
{% endhighlight %}


## Restriction by a string condition

Finally, `cond` could be a string containing a condition. These conditions are simply plugged into the SQL query and must be specified in SQL boolean expression syntax.

For example,

```
rel = common.TpSession & ...
    'anesthesia="fentanyl" and lens in ("25x","16x")' & ...
    (common.Animal & 'date_of_birth>"2012-06-01"')
```

will contain all two-photon imaging sessions that were performed under fentanyl anesthesia using the 25&times; or the 16&times; objective lens in which the subject was born after June 1, 2012.

### Restriction by a union of conditions
In the expression `rel = `original` & (cond1 | cond2)` the result `rel` will contain tuples from `original` that match either `cond1` or `cond2`, where `cond1` must be relvar but `cond2` can be a struct or a string.

Any number of conditions can be united, `rel = original - (cond1 | cond2 | cond3 ...)`.

Note that the expression `rel | cond` by itself does not produce a valid relation and can only be used to restrict another relation.

For example

```
rel = common.Animal & (psy.Session | common.TpSession)
```

will describe all animals that have participated in a psychophysics session or a two-photon session or both, which is logically equivalent to

```
rel = common.Animal - (common.Animal - psy.Session - common.TpSession)
```

Another example:

```
rel = common.Animal - (psy.Session | common.TpSession)
```

will describe all animals that did not participate in either kind of session, which is logically equivalent to

```
rel = common.Animal - psy.Session - common.TpSession
```



## Execution Speed
Logically equivalent relational expressions may take very different times to execute. It takes some experience to learn to devise fast expressions. This is a direct result of the structure of the underlying SQL queries.

For example,

```
A*B & 'condition on B'
```

often executes faster than

```
A & (B & 'condition on B')
```

even though the first expression has more attributes.


```
(A & 'condition on A')*B
```

often takes longer than

```
A*B & 'condition on A'
```
