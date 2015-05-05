---
layout: post
title: 'Base relvars'
tags: ['tutorial','matlab','python','help']
---

### What's a base relvar?
As [already discussed](Create a DataJoint Schema), a DataJoint schema is the pairing of a MySQL database with a MATLAB package.  

A _base relvar_ is a MATLAB class in a DataJoint schema associated with a table in the database. Users first create base relvars, which in turn automatically create their tables. Thus a base relvar provides a full interface to its table.

### Creating a new relvar
To create a new base relvar interactively, use the **dj.new** command:
```
>> dj.new
```
You will be prompted to enter the class name, its [tier](table tiers), and its [[foreign keys]]. The class name must be in format `package.ClassName`. The `ClassName` must start with a capital letter and only include alphanumerical characters.

Then **dj.new** will automatically create a new .m file with the complete definition of the class. For example, if we specified the class name `tp.Test` with tier `manual`, the class definition file will be
```
%{
tp.Test (manual) # my newest table

-----

%}

classdef Test < dj.Relvar
end
```

The first percent-brace comment block that starts with `%{` and ends with `%}` has special meaning in DataJoint. It contains the [[table declaration]].  Upon the first invocation of an object of this class, if the table is not yet created in the database, DataJoint will automatically create the table based on this declaration. 

The name of the created table will be similar to the name of the class with a prefix indicating the [table tier](table tiers). For example, the computed base relvar `tp.OrientationTuning` will create the database table `__orientation_tuning`, where the double-underscore prefix indicates that this is a computed table.  However, DataJoint users never need to address tables by their internal names.