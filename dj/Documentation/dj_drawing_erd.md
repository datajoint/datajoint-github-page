---
title: Entitity relationship diagram
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "Plot the relationship between tables in a schema"
sidebar: dj_sidebar
permalink: dj_drawing_erd.html
folder: dj/documentation
---



## Matlab

The entity relation ship diagram displays the tables in the schema and the foreign key dependencies between them. To view the entity relationship diagram of the entire DataJoint schema called `<package>`, type

{% highlight matlab %}
>> erd <package>
{% endhighlight %}

To view the diagram of the immediate neighborhood of a single [base relvar](base relvars), type

{% highlight matlab %}
>> erd <package>.<ClassName>
{% endhighlight %}

Since the ERD is a directed acyclic graph, all dependencies point upward and arrowheads are omitted. The font color indicates the tier of each table (<font color=gray>lookup</font>, <font color=green>manual</font>, <font color=blue>imported</font>, and <font color=red>computed</font>). And the asterisk * indicates a subtable. Solid edges indicate [hierarchical dependencies]( 2015-05-05-hierarchicaldependencies ) and dashed edges indicate non-hierarchical dependencies.

### Example
Review ERDs for the schemas in the [sessions repository](https://github.com/atlab/sessions)

TODO: insert ERD here or insert link to gallery entry

## Python

TODO
