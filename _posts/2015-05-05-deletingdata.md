---
layout: post
title: 'Deleting data'
tags: ['tutorial','matlab','python','help']
summary: 'How to delete data from tables.'
---

Deleting a set of tuples from a table will also recursively delete all corresponding tuples in all its [dependent tables]({% post_url 2015-05-05-foreignkeys %}).

To delete a subset of tuples, any kind of [restriction]({% post_url 2015-05-05-restrictions %}) can be applied to the base relvar before deleting. However, other operators such as [join]({% post_url 2015-05-05-join %}) or [projection]({% post_url 2015-05-05-projection %}) will produce a relation that cannot be deleted. 


## Matlab 

To delete a selection of tuples from a [base relvar]({% post_url 2015-05-05-baserelvars %}), use the `del` command. 

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

When precautions are unnecessary, a faster way to delete a selection of tuples from a table is the method `delQuick'. It is non-interactive and it does not cascade the delete down to dependent tables.

See 

```
help dj.BaseRelvar/del
help dj.BaseRelvar/delQuick
```

## Python

TODO