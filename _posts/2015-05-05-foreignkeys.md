---
layout: post
title: 'Foreign keys'
tags: ['tutorial','matlab','python','help']
summary: 'What are foreign keys'
---

## Definition

A [foreign key](http://en.wikipedia.org/wiki/Foreign_key foreign key) is a referential constraint which precludes the existence of a tuple in one table (dependent table) without there existing the matching tuple in a another (referenced) table.

DataJoint intentionally does not provide a way to update values of individual attributes in existing tuples in a table (SQL's UPDATE command). Instead, the entire tuple must be deleted. When a tuple is deleted, matching tuples in all dependent tables are deleted as well thereby ensuring that all computations performed in dependent tables are updated using the new values of all attributes in the referenced tables.

Foreign keys create a dependency hierarchy which is reflected by the [entity relationship diagram]({% post_url 2015-05-05-erd %}).

Adding a foreign key has the following effects:

* The primary key attributes of the referenced table are added to the declaration of the dependent table. For example, the primary key of `common.TpScan` in our example will become `(animal_id,tp_session,scan_idx)`.
* It will no longer be possible to enter data into `common.TpScan` before entering entries into `common.TpSession` with identical values of `(animal_id,tp_session)`.
* [Deleting data]({% post_url 2015-05-05-deletingdata %}) from `common.TpSession` will also delete all matching data from `common.TpScan`.
* [Dropping table]({% post_url 2015-05-05-droppingtables %}) `common.TpSession` will also drop table `common.TpScan`.

To enforce referential integrity, DataJoint does not provide the ability to updates individual field values in an existing tuple: To change the value of a tuple, one must be delete it first and then insert it again, allowing the database to check and enforce the referential constraints.

### Matlab Example

For example, table [`common.TpScan`](http://github.com/atlab/commons/blob/master/schemas/+common/TpScan.m) references table [`common.TpSession`](http://github.com/atlab/commons/blob/master/schemas/+common/TpSession.m). 
The line `-> common.TpSession` in the declaration of `common.TpScan` creates the foreign key constraint. 

### Python Example

The equivalent definition in python would look like


{% highlight python %}

import where_TpSession_lives

schema = dj.schema('common', locals())

@schema
class TpScan(dj.Manual):
		
	definition = """
	# scanimage scan info
	
	->where_TpSession_lives.TpSession
	scan_idx : smallint # scanimage-generated sequential number
	
	-----
	
	surfz               : float   # (um) z-coord at pial surface
	depth=0             : int     # manual depth measurement 
	laser_wavelength    : float # (nm)
	laser_power         : float # (mW) to brain
	cortical_area="V1"  : enum('other','unknown','V1','LM','AL','PM') # Location of scan
	scan_notes = ""     : varchar(4095)  #  free-notes
	scan_ts = CURRENT_TIMESTAMP : timestamp   # don't edit

	"""



{% endhighlight %}

Note that the foreign key is specified by the *local name* of the other table. If `common.TpSession` had been imported via `from where_TpSession_lives import TpSession as peter`, you would define the foreign key as `->peter`.