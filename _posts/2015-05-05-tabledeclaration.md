---
layout: post
title: 'Table declaration'
tags: ['tutorial', 'matlab', 'python', 'help']
summary: 'How to declare tables.'
---


The part that defines the structure of the table is the at the top of the files in Matlab and the class variables `definition` in Python. The syntax is the same in both cases. Let's look at it in more detail. 

```
	subject_id                   : int                     # id number
	---
	first=""                     : varchar(20)             # first name
	last=""                      : varchar(20)             # last name
	sex="unknown"                : enum('M','F','unknown') # animal's sex
	subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic

```
* **comments and empty** Everything after a `#` is ignored. Empty lines are ignored as well.

* **table identity**
	Table identity is defined differently in Matlab and Python. 

	In Matlab, the first non-blank line identifies the table as follows:

	```
	    <package>.<TableName>(<tier>)   # comment about the table
	```

	`<package>` denotes the name of the package in Matlab. `<TableName>` must match the name of the class. `<tier>` can be one of the following: `lookup`, `manual`, `imported`, or `computed`.  

	In Python, the table name is defined via the class name and the tier is defined by what this class inherits from (`dj.Manual`, `dj.Computed`, `dj.Lookup`, `dj.Imported`). That leaves only the comment to be specified by the first line in the table definition

	```python
		@schema
	    class MyClass(dj.<tier>):
	    	definition = """
	    	# comment about the table

	    	...
	    	"""
	```



	For more information see [table tiers](/2015/05/05/tabletiers/) in the tutorial section. 

The rest of the declaration defines table attributes. The declaration is separated into two parts separated by a line that starts with at least three minuses dashes `---`. 

* **primary key**
	All attributes above the separator make up the [primary key](/2015/05/05/primarykeys/). Primary keys uniquely identify a row (tuple) in a table. There can be no two tuples with the same set of primary key values in a table. Here, we just use an ID to uniquely identify our subjects. 

* **foreign keys**
	In many cases, you want to introduce a dependency on entries in other tables. For example, each trial is associated with a unique subject. This kind of relation is expressed with [Foreign keys](/2015/05/05/foreignkeys/). They are declared by including the line 


	```
	-> <package>.<AnotherBaseRelvar>
	```
	in Matlab. where `package.AnotherBaseRelvar` is the [base relvar](/2015/05/05/baserelvars/) class name of the referenced table. For example, in the `Trials` class, we define the foreign key `subj.Subjects`. However, we also need another primary key to allow for several trials. If the primary keys were just the foreign key, we could only have one trial per subject. 

	```
	subj.Trials (manual)                      # info about trials

	-> subj.Subjects
    trial_id                   : int          # trial id

	---
	outcome                    : int           # result of experiment

	notes=""                   : varchar(4096) # other comments 
	trial_ts=CURRENT_TIMESTAMP : timestamp     # automatic
	```


	In Python, we indicate foreign keys by

	```
	-> Subjects
	```
	You can use whatever name the relation class has in the local context. For instance, you could import a class called `Animals` from another schema via `from my_other_schema import Animals as LabAnimals` and then refer to it in the table definition as `->LabAnimals`. This approach is quite powerful and gives the user a lot of freedom how to define and group schemata. 



* **attributes**
	The attributes below the line define regular subordinate attributes. They are specified via:
	
	```
	attribute_name  :  datatype   # comment
	```
	
	or with a default value as 
	
	```
	attribute_name = default : datatype # comment
	```
	
	the attribute name must start with a lowercase letter and contain only lowercase letters, digits, and underscores.

	`datatype` is a MySQL datatype. Supported datatypes can be found [here](/datatypes/).

	When the default value is provided, tuples may be inserted without specifying that attribute and the default value will be inserted.  String values and enum values must be enclosed in double quotes `""`.   

	When the special value of `null` is specified as the default, the the attribute is specified as _nullable_. If a tuple is inserted without specifying that field, the value will be missing.  For string attributes, instead of specifying null, consider defaulting to `""`.  An empty string and null are two different things but empty strings are usually easier to work with. Primary key fields cannot be nullable. 

	The special value `CURRENT_TIMESTAMP` can be used as the default of `timestamp` fields, which is commonly used to timestamp the data.

* **secondary indices**

	You can add secondary (unique) indices to the end of your table declaration:

	```
	+++
	INDEX(attribute1)
	INDEX(attribute1,attribute2,attribute3)
	UNIQUE INDEX(attribute1)
	UNIQUE INDEX(attribute1,attribute2,attribute3)
	```

## Examples
Review examples of table declarations in the base relvars in our [gallery](/gallery/).