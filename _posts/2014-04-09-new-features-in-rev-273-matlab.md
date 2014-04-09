---
layout: post
title: "New features in rev 2.7.3 (Matlab)"
description: ""
category: releases
tags: [matlab, features, example]
---
{% include JB/setup %}
Attribute definitions no longer require comments
---
Until version 2.7.3 (MATLAB), attribute definitions in table declarations required a non-empty comment.  This was intended to encourage proper documentation.  However, in many cases a well-named attribute does not require a commment.  So attribute comments are now optional.

Here is an example of a table definition with some attribute comments ommitted

{% highlight matlab %}
% +world/City.m

%{ 
world.City  (manual)  #  world cities and their locations
city_id  :  int   # unique city ID  
----
city_name : varchar(50)
latitude  : float       # (degrees)
longitude : float
%}

classdef City < dj.Relvar
end
{% endhighlight %}

dj.Relvar/insert now accepts cell arrays
---
Matlab's cell arrays can be more conveniently specified than matrix arrays. Insert now accepts cell arrays with the columns of the array corresponding to the table's attributes, in sequence.

For example, to insert city information into **world.City** defined above, one might do something like
{% highlight matlab %}
insert(world.City,{
   55491  Lutsk     50.7593   25.3424
   94933  Brussels  44.7631  -87.6305
  133633  Brussels  38.9669  -90.5756
  204198  Tokyo     35.6850  139.7514
})
{% endhighlight %}

To check what version of DataJoint you are running now, type at the prompt
{% highlight matlab %}
>> dj.version
{% endhighlight %}
