---
title: Working with Data
keywords: dev
last_updated: July 3, 2016
tags: null
summary: ""
sidebar: dj_sidebar
permalink: dj_working_with_data.html
folder: dj/Documentation
---

## Inserting data

### Matlab
One can insert tuples into tables from MATLAB scripts using the `insert` command of a [base relvar](2015-05-05-baserelvars).

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

For [imported](2015-05-05-tabletiers) and [computed](2015-05-05-tabletiers) tables, one should only insert data from the `makeTuples` callback as part of DataJoint's standard process for [populating computed data]( 2015-05-05-populating).

See also

```matlab
help('dj.BaseRelvar/insert')
```

### Python

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

## Fetching Data
As discussed previously, [base relvars](2015-05-05-baserelvars %}) and derived relvars only represent the data in the database but do not themselves contain any of it. The `fetch` operators retrieve the data represented by relvars into the Matlab/Python workspace.

The fetch operators always perform a relational [projection]( 2015-05-05-projection) and accept the same arguments as the projection of the corresponding relvar.

## Matlab
For more details, see

``` matlab
>> doc dj.Relvar/pro
>> doc dj.Relvar/fetch
>> doc dj.Relvar/fetch1
>> doc dj.Relvar/fetchn
```
## Python
DataJoint Relations in Python provide the member functions `fetch1` and `fetch`. These return numpy structured arrays by default, but can also return a list of dictionaries. Fetching into pandas DataFrames is planned, but not implemented yet.

## Deleting data
Deleting a set of tuples from a table will also recursively delete all corresponding tuples in all its [dependent tables]( 2015-05-05-foreignkeys).

To delete a subset of tuples, any kind of [restriction]( 2015-05-05-restrictions) can be applied to the base relvar before deleting. However, other operators such as [join]( 2015-05-05-join) or [projection](2015-05-05-projection) will produce a relation that cannot be deleted.


### Matlab
To delete a selection of tuples from a [base relvar]( 2015-05-05-baserelvars), use the `del` command.

* deleting an entire table
	```
	del(tp.Segmentation)
	```

* deleting a subset of a table

	```
	del(tp.Segmentation & (common.TpSession & 'session_date >= "2013-01-01"'))
	```

	deletes all tuples from `tp.Segmentation` that match tuples from `common.TpSession` with `session_date` greater than `2013-01-01`. Or, in English, _delete all segmentations from two-photon sessions acquired since Jan 1, 2013_.

	Again, the delete will cascade to all the dependent tables.

A summary of data to be deleted is displayed to the user before the delete is executed. Once committed, the deletion is permanent and cannot be undone.

When precautions are unnecessary, a faster way to delete a selection of tuples from a table is the method `delQuick`. It is non-interactive and it does not cascade the delete down to dependent tables.

See also

```
help dj.BaseRelvar/del
help dj.BaseRelvar/delQuick
```

### Python
To delete a selection of tuples from a [base relvar]( 2015-05-05-baserelvars), you can use the objects function `delete`. For example, if `segm = tp.Segmentation()`, then

* deleting an entire table is carried out via the command
	```
	segm.delete()
	```

* deleting a subset of a table (`sess = common.TpSession()`)

	```
	(segm & (sess & 'session_date >= "2013-01-01"')).delete()
	```

	deletes all tuples from `segm` that match tuples from `sess` with `session_date` greater than `2013-01-01`. Or, in English, _delete all segmentations from two-photon sessions acquired since Jan 1, 2013_.

	Again, the delete will cascade to all the dependent tables.

**Warning:** In contrast to Matlab, Python will only display a summary or ask you to confirm the delete if you set `"safemode": true` in `dj_local_conf.json`.
