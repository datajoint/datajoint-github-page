---
layout: post
title: 'Base relations'
tags: ['tutorial','matlab','python','help']
summary: 'What are base relations?'
---

A [Base relvar object](/2015/05/05/baserelvars/) represents the relation containing the entire contents of its table. 

Other relations can now be derived from base relvars using relational operators. Note that the relvar objects do not themselves contain any data. They only form the specific query that can be  used to [fetch data](/2015/05/05/fetchingdata/) into the current workspace.


## Matlab

The display `display` method shows few tuples from the table and the total count of tuples.  For example, simply typing the class name of `common.Animal` from [schema "common"](example schemas) will display

{% highlight matlab %}
>> common.Animal

self = 

  common.Animal handle
  Package: common

  Properties:
           table: [1x1 dj.Table]
          schema: [1x1 dj.Schema]
          header: [6x1 struct]
             sql: '`common`.`animal`'
      primaryKey: {'animal_id'}
    nonKeyFields: {'real_id'  'date_of_birth'  'sex'  'animal_notes'  'animal_ts'}
    restrictions: {}

  Methods, Events, Superclasses


     animal_id       real_id  date_of_birt           sex  animal_notes     animal_ts
             0             0    2001-01-01       unknown  testing dumm  2012-04-17 0
             4   10575:33922    0000-00-00       unknown  Keith's GCaM  2012-07-11 1
           408           408    2011-04-11             F        C57BL6  2012-03-23 0
           436           325    2012-02-15             M                2012-03-24 1
           444           444    2012-02-21             F        C57BL6  2012-03-21 1
           445           445    2012-02-21             F        C57BL6  2012-03-22 0
           467           467    2012-02-24             M        C57BL6  2012-03-27 1
           476           476    2012-03-02             M        C57BL6  2012-03-29 1
           477           477    2012-03-02             M                2012-04-05 1
           480           480    2012-03-11             M        C57BL6  2012-04-16 1
           481           481    2012-03-11             M        C57BL6  2012-04-12 1
           482           482    2012-03-11             M                2012-04-17 1
         .....         .....         .....         .....         .....         .....
45 tuples (0.0382 s)

{% endhighlight %}

## Python

In python you can simply call `print(animal)` if `animal` is an instance of the `common.Animal` schema. This will print a similar output to the prompt. 

