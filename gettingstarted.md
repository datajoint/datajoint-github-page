---
layout: page
title: Getting Started
group: navigation
weight: 2
---

# Before you start

Make sure that you corretly [installed DataJoint](/installation.html).

If you have never heard of databases before, take a quick look at [basic database concepts](/basicconcepts.html).

# Define how you data is organized: Create a Schema

## What is a schema?
A DataJoint organizes data in _schemata_: a collection of logically related tables hosted on the database server. The word 'schema' is synonymous with 'database' in this context. We recommend that you use one schema for each project. However, you can also use a single schema for the entire lab. Moreover, DataJoint easily supports projects that span multiple schemas. For example, it is very convenient to set up a common schema for the acquisition and pre-processing of data while data analysis may be performed in separate schemas.



How you define a schema depends on whether you use the Matlab or the Python package:

* In Matlab, each schema has its own _namespace_ or _package_. Here is the [MathWorks page about packages](http://www.mathworks.com/help/matlab/matlab_oop/scoping-classes-with-packages.html). 
* In Python each schema has its own _module_ or _package_.

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

	properties(Constant)
		table = dj.Table('subj.Subjects')
	end

	methods
		function self = Subjects(varargin)
			self.restrict(varargin)
        end
        function makeTuples(self,key)
            self.insert(key)
        end
	end
end

{% endhighlight %}

Additionally we create a table called `Trials` that stores the outcome of your experiment. (let's assume it is just a single number). Create a file called `+subj/Trials.m` and add

{% highlight matlab %}
%{
subj.Trials (manual)                      # info about trials

-> subj.Subjects
---
outcome                    : int           # result of experiment

notes=""                   : varchar(4096) # other comments 
trial_ts=CURRENT_TIMESTAMP : timestamp    # automatic
%}

classdef Trials < dj.Relvar

	properties(Constant)
		table = dj.Table('subj.Trials')
	end

	methods
		function self = Trials(varargin)
			self.restrict(varargin)
        end
        function makeTuples(self,key)
            self.insert(key)
        end
	end
end

{% endhighlight %}

### Python	


Let's call the module for Python `subj` as well. To create it, you need to creat a file `subj.py`.

Inside this file put 

{% highlight python %}

import datajoint as dj

conn = dj.conn()
conn.bind(__name__, 'subjects')

class Subjects(dj.Base):
	definition = """
	subj.Subjects (manual) # Basic subject info

	subject_id                   : int                     # id number
	---
	first=""                     : varchar(20)             # first name
	last=""                      : varchar(20)             # last name
	sex="unknown"                : enum('M','F','unknown') # animal's sex
	subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic
	"""

class Trials(dj.Base):
	definition = """
	subj.Trials (manual)                      # info about trials

	-> subj.Subjects
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

#### Defining the structure

The part that defines the structure of the table is the at the top of the files in Matlab and the class variables `definition` in Python. The syntax is the same in both cases. Let's look at it in more detail. 

```

	subj.Subjects (manual) # Basic subject info

	subject_id                   : int                     # id number
	---
	first=""                     : varchar(20)             # first name
	last=""                      : varchar(20)             # last name
	sex="unknown"                : enum('M','F','unknown') # animal's sex
	subject_ts=CURRENT_TIMESTAMP : timestamp               # automatic
```

The important part is the comment above the class definition. It states the name of the table (`subj.Subjects`), its primary key(s) `subject_id` and the datatype of the primary key. Everything behind the `#` is treated as comment. 
`getSchema` basically connects your current package `subj` to your database `subjects`. Each package for DataJoint must have a function called `getSchema` that tells the package which database it should connect (bind) to. 
