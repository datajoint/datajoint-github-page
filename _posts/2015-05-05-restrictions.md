---
layout: post
title: 'Restrictions'
tags: ['tutorial','matlab','python','help']
summary: 'Restriction operator.'
---

[Relational restriction](http://en.wikipedia.org/wiki/Selection_%28relational_algebra%29) selects the subset of tuples that meet a given condition.

In DataJoint, relational restriction is implemented using MATLAB operators `&` and `-`. 

The following expression assigns to `rel` all the tuples from `original` that match condition `cond`.

```
rel = original & cond
```

The following expression assigns to `rel` all the tuples from `original` that do not match condition `cond`.

```
rel = original - cond
```

The condition `cond` can be another relvar, a structure array, or string containing a condition.

## Matlab

### Restriction by another relvar: _semjoin_ and _antijoin_

Recall that two tuples _match_ when their identically named attributes contain equal values.  When `cond` is another relvar, the result of `original & cond` will contain all tuples for which there exist a matching tuple in `cond`.

For example, referring to schema `common` and schema `psy`. 

```
rel = (common.Animal & psy.Session) - common.TpSession
```
the resulting relation `rel` will contain all animals that have participated in at least one psychophysics session but have not had any two-photon imaging performed on them.

If `cond` does not have any attributes with the same names as in `original`, then `rel` will be equal to `original`. 

In formal [relational algebra](http://en.wikipedia.org/wiki/Relational_algebra), restrictions by another relation are known as [semijoin](http://en.wikipedia.org/wiki/Relational_algebra#Semijoin_.28.E2.8B.89.29.28.E2.8B.8A.29) (`&`) and [antijoin](http://en.wikipedia.org/wiki/Relational_algebra#Antijoin_.28.E2.96.B7.29) (`-`).

### Restriction by a structure array
Relations can be represented in MATLAB by structure arrays.  Restriction by a structure array has the same meaning as restriction by another relvar.  

For example,

```
rel = common.TpSession & struct('animal_id', 14526)
```

returns all two-photon sessions for animal 14526.

Restriction by a structure plays an important role for addressing tuples or for iterating over tuples. For example, to review groups of tuples in `common.TpScan' for each `common.TpSession`, the following code could be used:

```
% iterate through two-photon scans
for key = fetch(common.TpSession)'   % primary key values of two-photon sessions
    rel = common.TpScan & key;
    % rel now represents all scans for the session identified by key
    ... do something with it here
end
```

### Restriction by a string condition

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

## Python

TODO