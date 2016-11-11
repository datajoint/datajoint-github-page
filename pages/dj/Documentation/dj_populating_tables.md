---
title:  'Populating Tables'
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "Populating tables with computed data"
sidebar: dj_sidebar
permalink: dj_populating_tables.html
folder: dj/documentation
---



Computing new data in DataJoint is a form of the [extend operator](https://github.com/datajoint/datajoint-matlab/wiki/Projection#extend): an existing relation is extended to include new computed attributes. The existing relation will here will be called the *populate relation* or `popRel`. The extended relation is then stored in a new computed table with a corresponding class.


## Creating a computed table

### Matlab
You may use the `dj.new` utility to create a new computed class or create on manually.

To configure a table to be populated automatically, its class must have three additional features:

1. inherit from class `dj.AutoPopulate`
2. define the constant property `popRel` (populate relation)
3. define the protected method `makeTuples(self, key)`

For example the automatically populated table `tp.Align` has the following definition:

```
%{
   (skipped) table definition here
%}

classdef Align < dj.Relvar & dj.AutoPopulate
    properties
         popRel = common.TpScan
    end

    methods (Access = protected)
        function makeTuples(self, key)
           % fetch info from other tables here
           [d1,d2] = fetchn(OtherTable & key, 'field1', 'field2')

            % compute the required fields here
            key.extra_field1 = ....
            key.extra_field2 = ...
            self.insert(key)
        end
   end
end
```

### Python

For Python, the naming is different but the basic mechanisms are the same

{% highlight python %}

class Align(dj.Computed):
  definition = """
  # skipped table definition here
  """

  @property
  def populated_from(self):
    return common.TpScan


  def _make_tuples(self, key):
    # fetch data and extend key
    # ...
    self.insert1(key)


{% endhighlight %}


## Populating the table

### Matlab
Once these elements have been defined. Users may populate the table by calling the dj.AutoPopulate's method `populate` (for debugging or small-scale processing) or `parpopulate` (for distributed processing with job management).

For example, to populate the table defined above:

```
>> populate(tp.Align)
```

The populate methods call `makeTuples(self,key)` once with every primary key value of `tp.Align.popRel` for which the table does not yet have any matching tuples.

The populate method accepts arguments that are applied to further restrict the populate relation. For example, to populate `tp.Align` only for `animal_id=107`:

```
>> populate(vis2p.ScansCorrected, 'mouse_id=107')
```

### Python

For Python, the mechanisms are the same, but the syntax is a little different

{% highlight python %}
Align().populate()
{% endhighlight%}



## Defining the popRel/ populated_from

### Matlab

The populate relation is the relvar for which the current table must have matching tuples. In most cases, this is simply the join of the table's immediate ancestors in the hierarchy, but not always.

### Python

In Python, the populate relation is implemented by the property `populated_from`. If this function is not implemented, the default behaviour is the join of the table's immediate ancestors in the hierarchy.

## Defining makeTuples/ \_make_tuples


The `makeTuples` callback must fill out the remaining attributes and insert the newly formed tuple or tuples into the table using the command self.insert(key).

The makeTuples callback consists of the following sections:

1. fetch related data from parent tables or parents' parent tables for the given key
2. compute the missing fields in the structure key
3. call self.insert(key)

**Important**: for referential integrity, `makeTuples()` should only retrieve data from tables that are direct ancestors of the present table and their ancestors' subtables. Fetching from tables that are not above the current table in the hierarchy may result in outdated tuples when the referenced data are changed. If data are needed from other tables, the schema should be updated to place the desired data above the current table. This constraint is not physically enforced by DataJoint for the sake of performance, making this the user's responsibility.


## Subtables

A subtable is an imported or computed table that is populated by its ancestor in the table hierarchy. As such, a subtable's relvar class does not inherit from dj.AutoPopulate and does not define a populate relation. It may still define some kind of makeTuples callback, however. Their makeTuples callback must be called directly from a parent's makeTuples/\_make_tuples. Thus subtable tuples are always inserted together with their parent tuples in one atomic transaction. In addition, DataJoint disables direct deletes from subtables so that, to delete from a subtable, one must delete from its parent table.

Subtables enable many-to-one dependencies. Matching tuples in a table and its subtable can be considered as an inseparable group. Another table can now refer to a parent tuple to establish a dependency on the matching group of tuples in the subtable. For example, a single image segmentation, represented by a tuple in table Segmentations results in multiple image masks represented by tuples in Masks, which will appear all together with its parent tuple or not at all. The Segmentations/makeTuples then calls the Masks/makeTuples after inserting its own tuple:


### Matlab


{% highlight matlab %}
% Segmentations/makeTuples
function makeTuples(self, key)
  self.insert(key)
  makeTuples(Masks, key)
  makeTuples(MaskSorts, key)
{% endhighlight %}


### Python

{% highlight python %}
# Segmentations/makeTuples
def _make_muples(self, key):
  # computation happens
  self.insert(key)
  Masks().makeTuples(key)
  MaskSorts().makeTuples(key)
{% endhighlight %}

## Transaction processing

### Matlab

Note that makeTuples calls are performed within individual atomic transaction: Inserted tuples do not become visible to the rest of the world until makeTuples exits without errors. If makeTuples fails to complete, any inserts that it has already inserted into the table or its subtables are rolled back and never become visible to other processes. Conversely, changes made by other processes do not become visible to the makeTuples call while it is executing, making the world appear stable within a makeTuples call.

Transaction processing makes DataJoint transaction-safe: interrupted or failed processes do not leave incomplete data behind.
