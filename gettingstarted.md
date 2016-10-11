---
layout: page
title: Getting Started
group: navigation
weight: 2
---

# Before you start

Check out the DataJoint [whitepaper](http://biorxiv.org/content/early/2015/11/14/031658).

Make sure that you correctly [install DataJoint](/installation.md).

# Define how you data is organized: Create a Schema

## What is a schema?
A DataJoint organizes data in _schemata_: a collection of logically related tables hosted on the database server. The word 'schema' is synonymous with 'database' in this context. We recommend that you use one schema for each project. However, you can also use a single schema for the entire lab. Moreover, DataJoint easily supports projects that span multiple schemas. For example, it is very convenient to set up a common schema for the acquisition and pre-processing of data while data analysis may be performed in separate schemas.



How you define a schema depends on whether you use the Matlab or the Python package:

* In Matlab, each schema has its own _namespace_ or _package_. Here is the [MathWorks page about packages](http://www.mathworks.com/help/matlab/matlab_oop/scoping-classes-with-packages.html). 
* In Python there is no such restriction. However, we still recommend putting all objects for one schema in one file. 

A _DataJoint schema_ is the pairing of a MySQL schema and Matlab/Python package. Therefore, by _schema_ we will often mean such a pairing and not just the database.


## Create a DataJoint schema

Now let's set up a schema. Assume that you have a database `subjects` in MySQL in which you plan to store some information about trials in a psychophysical experiment. The first task is to define the structure of the tables in that schema. We will just state the code for Matlab and Python, and then explain what it means below. 

### Matlab

Let's call the package on the Matlab side `subj`. To create it, you need to creat a folder `+subj` and inside that folder you need to create a function `getSchema` in a file called `+subj/getSchema.m`. 

{% highlight matlab %}
function obj = getSchema
persistent schemaObject

if isempty(schemaObject)
    schemaObject = dj.Schema(dj.conn, 'subj', 'subjects');
end

obj = schemaObject;
end
{% endhighlight %}


We also need to define a table for your subjects. To that end, create a file called `+subj/Subjects.m` and put the following content in it
{% highlight matlab %}
%{
subj.Subjects (manual) # Basic subject info

subject_id                   : int                     # id number
---
first=""                     : varchar(20)             # first name
last=""                      : varchar(20)             # last name
sex="unknown"                : enum('M','F','unknown') # animal's sex
subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic
%}

classdef Subjects < dj.Relvar

end

{% endhighlight %}

Additionally we create a table called `Trials` that stores the outcome of your experiment. (let's assume it is just a single number). Create a file called `+subj/Trials.m` and add

{% highlight matlab %}
%{
subj.Trials (manual)                      # info about trials

-> subj.Subjects
trial_id                   : int          # trial id

---
outcome                    : int           # result of experiment

notes=""                   : varchar(4096) # other comments 
trial_ts=CURRENT_TIMESTAMP : timestamp    # automatic
%}

classdef Trials < dj.Relvar

end

{% endhighlight %}

### Python	


Let's call the module for Python `subj` as well. To create it, you need to creat a file `subj.py`.

Inside this file put 

{% highlight python %}

import datajoint as dj

schema = dj.schema('subjects', locals())

@schema
class Subjects(dj.Manual):
	definition = """
	# Basic subject info

	subject_id                   : int                     # id number
	---
	first=""                     : varchar(20)             # first name
	last=""                      : varchar(20)             # last name
	sex="unknown"                : enum('M','F','unknown') # animal's sex
	subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic
	"""

@schema
class Trials(dj.Manual):
	definition = """
	# info about trials

	-> Subjects
    trial_id                   : int          # trial id
	---
	outcome                    : int           # result of experiment

	notes=""                   : varchar(4096) # other comments 
	trial_ts=CURRENT_TIMESTAMP : timestamp    # automatic
	"""


{% endhighlight %}

### Explanation

The above example has basically two parts:

* defining the structure of the table
* connecting the table to the database

#### Connecting the table to the database

Each class needs to know where to find the tables it represents. DataJoint already know which MySQL server it connects to. Now we need to tell it which database holds the tables that our classes ([relvars](/2015/05/05/baserelvars/)) represent.

In Matlab, the function `+subj/getSchema.m` connects the database `subjects` to the package `subj`. Each package must have a `getSchema.m` function. 

In Python, the line

```
schema = dj.schema('subjects', locals())
```

creates a decorator for all classes that represent tables from the database `subjects`. You can create as many decorators as you want and connect different classes in the module to different databases on the server. The second argument `locals()` provides DataJoint with information about other objects that are referenced in the table definition. It basically needs to be a dictionary that maps `FOREIGNKEYNAME` to the respective object. `locals()` will work in almost all situations. 


#### Defining the structure

The part that defines the structure of the table is the at the top of the files in Matlab and the class variables `definition` in Python. The syntax is very similar for Matlab and Python, but not identical. 

Matlab requires you to specify the package, the table name, and the [table tier](/2015/05/05/tabletiers/). This is done in the first line as follows:

```
    <package>.<TableName>(<tier>)   # comment about the table
```

`<package>` denotes the name of the package in Matlab. `<TableName>` must match the name of the class. `<tier>` can be one of the following: `lookup`, `manual`, `imported`, or `computed`.  For more information see [table tiers](/2015/05/05/tabletiers/) in the tutorial section. For now, we simply use `manual`. Additionally, you need to provide a table comment describing what is stored in the table. 


```
	subj.Subjects (manual) # Basic subject info
	....

```

In Python, the [table tier](/2015/05/05/tabletiers/) and the table name are specified by the class definition itself

{% highlight python %}
@schema
class Subjects(dj.Manual):
	# ...
{% endhighlight %}


Therefore, the table definition only needs to provide the table comment

```

	# Basic subject info

	...
```


The remaining part of the table definition defines the structure of the table. Let's look at it in more detail. 

* **comments and empty lines** `#` denote optional comments. Everything behind it is ignored. Empty lines are ignored as well.

```

	...

	subject_id                   : int                     # id number
	---
	first=""                     : varchar(20)             # first name
	last=""                      : varchar(20)             # last name
	sex="unknown"                : enum('M','F','unknown') # animal's sex
	subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic

```

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

	When the special value of `null` is specified as the default, the attribute is specified as _nullable_. If a tuple is inserted without specifying that field, the value will be missing.  For string attributes, instead of specifying null, consider defaulting to `""`.  An empty string and null are two different things but empty strings are usually easier to work with. Primary key fields cannot be nullable. 

	The special value `CURRENT_TIMESTAMP` can be used as the default of `timestamp` fields, which is commonly used to timestamp the data.

## Insert data into the schema

### Matlab

One can insert tuples into tables from MATLAB scripts using the `insert` command of a [base relvar](/2015/05/05/baserelvars/). 

For example, inserting a new subject from a matlab script 

{% highlight matlab %}

insert(subj.Subjects, struct(...
    'subject_id ', 1, ...
    'first', 'John', ...
    'last', 'Doe', ...
    'sex', 'M'))

{% endhighlight %}

If the table does not yet exist in the database, DataJoint automatically creates it and inserts the data. 

Insert also supports multiple tuples

{% highlight matlab %}

insert(subj.Trials, cell2struct({
         1        1		0 	"equipement was not working" 
         1        2 	0 	""
         1        3 	1 	""
}',  {'subject_id'  'outcome' 'notes'})

{% endhighlight %}

### Python

In python, we instantiate a relvar object and call the insert function to insert values

{% highlight python %}

import datajoint as dj
from subj import Subjects, Trials

s = Subjects()
t = Trials()

s.insert1({'subject_id':1, 'first':'John', 'last':'Doe', 'sex':'M'}) # insert a single subject

{% endhighlight %}

`insert1` supports _dictionaries_, and _numpy structured arrays_. 

Similarly, we could insert single trials. For adding multiple rows at once, we use the `insert` function

{% highlight python %}

t.insert([dict(subject_id=1, trial_id=2,outcome=0,notes='equipment was not working'), \
		  dict(subject_id=1, trial_id=3, outcome=0)])

{% endhighlight %}

## Querying and retrieve data

Let's assume we already entered some data in the tables. `Subjects` and `Trials` look now like

```

subject_id     first          last           sex            subject_ts    
+------------+ +------------+ +------------+ +------------+ +------------+
1              John           Doe            M              2015-05-14 11:
2              Joan           Doe            F              2015-05-14 11:
 (2 tuples)

subject_id     trial_id       outcome        notes          trial_ts      
+------------+ +------------+ +------------+ +------------+ +------------+
1              0              6              no comment     2015-05-14 11:
2              0              7              no comment     2015-05-14 11:
1              1              7              no comment     2015-05-14 11:
2              1              8              no comment     2015-05-14 11:
1              2              9              no comment     2015-05-14 11:
2              2              5              no comment     2015-05-14 11:
1              3              8              no comment     2015-05-14 11:
...
 (40 tuples)


```

Now we can start to query for certain datapoints. For example, assume we want to exclude the first five trials for each subject from our analysis. 

This can be done by a [restriction]({% post_url 2015-05-05-restrictions %}). In both Matlab and Python the command is

```
t5 = t & 'trial_id > 5'
```

`t5` now represents a new relation that keeps all the trials with `trial_id > 5`. If you print it to the promt you'll see

```
subject_id     trial_id       outcome        notes          trial_ts      
+------------+ +------------+ +------------+ +------------+ +------------+
1              6              2              no comment     2015-05-14 11:
2              6              5              no comment     2015-05-14 11:
1              7              3              no comment     2015-05-14 11:
2              7              5              no comment     2015-05-14 11:
1              8              1              no comment     2015-05-14 11:
2              8              5              no comment     2015-05-14 11:
1              9              2              no comment     2015-05-14 11:
...
 (28 tuples)

```

Note that the data is not fetched or kept in `t5`. To actually retrieve the data, we need the command

{% highlight matlab %}

data = fetch(t5)

{% endhighlight %}


in Matlab or 

{% highlight python %}

data = t5.fetch()

{% endhighlight %}

in Python. This command fetches the data form the database and writes it in the variable data. Note that this is highly efficient: We don't copy the data into the workspace before we actually decided what data we want. 

Another way we could have written this restriction is 

```
t5 = t - 'trial_id <= 5'
```

which just excludes all trials that match the condition. 

The second term in restrictions does not need to be a string. It can also be another relation or even a struct in Matlab or a dictionary in Python.

For instance, in Python 

```
t5 = t & (s & "sex='M'") - "trial_id < 5"
```

yields

```
subject_id     trial_id       outcome        notes          trial_ts      
+------------+ +------------+ +------------+ +------------+ +------------+
1              5              2              no comment     2015-05-14 11:
1              6              2              no comment     2015-05-14 11:
1              7              3              no comment     2015-05-14 11:
1              8              1              no comment     2015-05-14 11:
1              9              2              no comment     2015-05-14 11:
1              10             7              no comment     2015-05-14 11:
1              11             3              no comment     2015-05-14 11:
...
 (15 tuples)
```

which is all the trials from subject 1, who is male, starting with trial ID 5.

# How to go on from here

Now you know how to create relations, enter data, and retrieve data in DataJoint. However, these are just the very basics. DataJoint can do much more. We have a tutorial section where you can read up on 

* the full power of [restrictions]({% post_url 2015-05-05-restrictions%}).
* how to combine relations in a smart way using [join]({% post_url 2015-05-05-join%}).
* how to [rename, add, project out, and aggregate]({% post_url 2015-05-05-projection%}) columns (attributes).
* how to [modify]({% post_url 2015-05-05-modifyingtables %}) tables.
* how to [delete]({% post_url 2015-05-05-deletingdata %}) data from your database or how to [drop]({% post_url 2015-05-05-droppingtables%}) entire tables. 
* how to [populate]({% post_url 2015-05-05-populating%}) tables with automatically computed data. 
* many more [topics](/tutorials/).