---
layout: post
title: 'Table declaration'
tags: ['tutorial', 'matlab', 'python', 'help']
summary: 'How to declare tables.'
---

In DataJoint, table declarations must be provided in the first percent-brace comment block of their [[base relvars]]. This section describes the syntax of table declaration within these blocks.

For examples, review some classes in the schemas `acq`, `ephys`, or `aod` in the [sessions repository](https://github.com/atlab/sessions).

First, all blank lines are ignored. 

## table identity
The first non-blank line identifies the table as follows:
```
    <package>.<TableName>(tier)   # comment about the table
```

The table name must match the class name. 

`tier` can be one of the following: `lookup`, `manual`, `imported`, or `computed`.  See [[table tiers]].

Finally the comment is an arbitrary string describing the purpose of the table.  The comment should not contain any double quotes `"`

## primary key  
The rest of the declaration defines table attributes. The declaration is separated into two parts by a line that starts with at least three minuses `---`. All attributes above the separator make up the [primary key](primary keys). The attributes below the line define regular subordinate attributes.

## foreign keys 
Foreign keys are declared by including the line  
```
-> package.AnotherBaseRelvar
```
where `package.AnotherBaseRelvar` is the base relvar class name of the referenced table.

The effect of adding a foreign key to a Relvar is:

1. The primary key fields of the referenced table are added to the current table (except those that are already included.  Just as with other fields, the new fields are added as primary key fields if the reference is made above the separator and as subordinate fields if below the separator.
1. A [foreign key constraint](foreign keys) is set to the referenced table.

## attributes
Other lines of the declaration specify individual attributes as:
```
attribute_name  :  datatype   # comment
```
or with a default value as 
```
attribute_name = default : datatype # comment
```
the attribute name must start with a lowercase letter and contain only lowercase letters, digits, and underscores.

`datatype` is a MySQL datatype. See the supported [[datatypes]].

When the default value is provided, then tuples may be inserted without specifying that attribute and the default value will be inserted.  String values and enum values must be enclosed in double quotes `""`.   

When the special value of `null` is specified as the default, the the attribute is specified as _nullable_.  Primary key fields cannot be nullable.  If a tuple is inserted without specifying that field, the value will be missing.  For string attributes, instead of specifying null, consider defaulting to `""`.  An empty string and null are two different things but empty strings are usually easier to work with.

The special value `CURRENT_TIMESTAMP` can be used as the default of `timestamp` fields, which is commonly used to timestamp the data.

## secondary indices

You can add secondary (unique) indices to the end of your table declaration:
```
+++
INDEX(attribute1)
INDEX(attribute1,attribute2,attribute3)
UNIQUE INDEX(attribute1)
UNIQUE INDEX(attribute1,attribute2,attribute3)
```

## examples
Review examples of table declarations in the base relvars in the [sessions repository](https://github.com/atlab/sessions/tree/master/schemas).