---
layout: post
title: 'Entering data'
tags: ['tutorial','matlab','python','help']
summary: 'How to insert data in tables'
---

## Matlab

One can insert tuples into tables from MATLAB scripts using the `insert` command of a [base relvar]({% post_url 2015-05-05-baserelvars %}). 

For example, insert information about an animal from the MATLAB command line.

```matlab
insert(common.Animal, struct(...
    'animal_id', 13, ...
    'date_of_birth', '2012-07-01', ...
    'animal_notes', 'test dummy'))
```

or insert multiple tuples 


```matlab
insert(common.Animal, cell2struct({
         13        '2012-07-01'      'M'   'test dummy'
         14        '2012-07-02'      'F'   'another test'
         15        '2012-08-01'      'M'   ''
}',  {'animal_id'  'date_of_birth'  'sex'  'animal_notes'})
```

For [imported]({% post_url 2015-05-05-tabletiers %}) and [computed]({% post_url 2015-05-05-tabletiers %}) tables, one should only insert data from the `makeTuples` callback as part of DataJoint's standard process for [populating computed data]({% post_url 2015-05-05-populating %}).

See also

```matlab
help('dj.BaseRelvar/insert')
```

## Python 

The equivalent calls in Python would be

```python
common.Animal.insert1(
		dict(animal_id=13, date_of_birth='2012-07-01', sex='M', animal_notes='test dummy'))
```

or insert multiple tuples 


```python

common.Animal.insert([
	dict(animal_id=13, date_of_birth='2012-07-01', sex='M', animal_notes='test dummy')),
	dict(animal_id=14, date_of_birth='2012-07-02', sex='F', animal_notes='another test')),
	dict(animal_id=15, date_of_birth='2012-08-01', sex='M', animal_notes=''))
	])

```