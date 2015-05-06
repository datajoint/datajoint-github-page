---
layout: page
title: Home
tagline: data processing chains for science labs
weight: 0
---

# WORK IN PROGRESS

This website is currently work in progress. The final version and new releases of the libraries should be up by mid of May 2015. 

# In a hurry?

Check out these links

* Getting started
* Introductory tutorial
* Schemata gallery
* API for Matlab and Python

# What is DataJoint?

DataJoint for [MATLAB](http://datajoint.github.io/datajoint-matlab/) and [Python](https://github.com/datajoint/datajoint-python) is a high-level programming interface for MySQL databases to support data processing chains in science labs. DataJoint is built on the foundation of the relational data model and prescribes a consistent method for organizing, populating, and querying data.

DataJoint was initially developed in 2009 by [Dimitri Yatsenko](https://github.com/dimitri-yatsenko) (Baylor College of Medicine) for the distributed processing and management of large volumes of data streaming from regular experiments. Starting in 2011, DataJoint has been available as an open-source project adopted by other labs and substantially improved with  contributions from [Andreas Hoenselaar](https://github.com/ahoenselaar) (CalTech), [Alex Ecker](https://github.com/aecker) (Max Planck Institute for Biological Cybernetics), [Edgar Walker](https://github.com/eywalker) (Baylor College of Medicine), and [Fabian Sinz](https://github.com/fabiansinz) (Baylor College of Medicine) . DataJoint was inspired in part by an earlier database tool called Steinbruch developed by [Alex Ecker](https://github.com/aecker) and [Philipp Berens](https://github.com/philippberens). 

## Designed for busy scientists and collaborative data sharing
The amount of data produced by scientific labs has been steadily increasing. Ensuring long-term accessibilty and reproducibility of results with data aquired by several people, sometimes in several different labs, has become a challenge. In many labs, the default data management scheme comprises the OS file system with clever naming conventions with additional meta data stored in CSV, XML, .MAT files. This works fine for dead storage, but makes more interesting queries difficult, provides no easy way to maintain data dependencies, and becomes problematic when data is shared across labs.

For example, imagine that you want to compute a summary of all experiments that have been done in your lab during the last 7 years using a particular experimental setting. With a data management scheme that relies on naming of folders you are likely to spend a good amount of time on crawling the different directories and extracting the relevant datasets. Since the naming scheme is never enforced, single persons can (and probably will) violate it. Unfortunately, every deviation from it will require you to either include extra cases in your script or to fix the naming manually. The latter case will almost certainly break the analysis code of some other person in your lab. 

In DataJoint, the data for the above task is just one line of code. DataJoint provides a flexible, light-weight, yet capable system that allows you to organize your data and quickly access it. Even better, it is not hard to learn and use. 


## DataJoint is based on the relational data model and object-relational mapping

We know what you are thinking: *There you promised us something that is easy to learn and use, and here the next heading is only comprehensible by people with a computer science major*. However, the essence of these two central principles of DataJoint is natural and easy to understand:

### Data is organized in tables (relational data model):

Organizing data in tables is quite natural. Everyone who already worked with Excel or .csv files has experience with it. The only crucial difference to a relational data model is that each row in a table has to be unique. Those kind of tables are traditionally called *relations*. The relational data model was invented by IBM researcher [Edgar F. Codd](http://en.wikipedia.org/wiki/Edgar_F._Codd) in 1969, (E. F. Codd. "A relational model of data for large shared data banks." _Communications of the ACM_, 13(*6*):387, 1970) the [relational model](http://en.wikipedia.org/wiki/Relational_model) for databases steadily replaced the earlier hierarchical and network models and has become the de facto standard for mainstream databases today, supporting banking transactions, airfare bookings, and data-intensive websites such as Facebook, Google, Wikipedia, and YouTube, to pick but a few examples.  Modern relational database management systems  execute fast, precise, and flexible data queries and preclude inconsistencies arising from simultaneous or interrupted manipulations by multiple users. Interactions with a relational database are performed in a query language such as [SQL](http://en.wikipedia.org/wiki/SQL SQL). 


### Database tables are hidden behind Matlab/Python objects (object-relational mapping)

DataJoint stores data in regular MySQL tables. Why MySQL? Because it is optimized for storing and retrieving data, and it ensures a wide accessibility of the data. Any other language that has a MySQL API can be adapted to exchange data with DataJoint applications. 

Unfortunately, SQL queries are typically cumbersome. SQL requires significant training to be used effectively. DataJoint makes it easy for you to deal with the tables by hiding them behind Matlab/Python objects. All data definition and manipulation tasks are performed from Matlab/Python using Matlab/Python language constructs. DataJoint associates each table in the database with a Matlab/Python class. Users manipulate data by invoking these classes that encapsulate the corresponding tables. This is called [object-relational mapping](http://en.wikipedia.org/wiki/Object-relational_mapping).

## Creating tables with DataJoint is simple
DataJoint provides its own syntax for creating tables directly from Matlab/Python code. Tables are created automatically upon the first invocation of the table in application code.

For example, a table [psy.Trial](https://github.com/dimitri-yatsenko/dj-schema-psy/blob/master/Trial.m) defining visual stimulus trials would be defined in Matlab simply as a comment in a file called *Trial.m*

{% highlight matlab %}
%{
psy.Trial (manual) # visual stimulus trial
-> psy.Session
trial_idx       : int         # trial index within sessions
---
-> psy.Condition
flip_times                  : mediumblob          # (s) row array of flip times
last_flip_count             : int unsigned        # the last flip number in this trial
trial_ts=CURRENT_TIMESTAMP  : timestamp           # automatic
%}
{% endhighlight %}

In python, the corresponding definition looks like
{% highlight python %}
import datajoint as dj

class Trial(dj.Base):
	definition = """
	psy.Trial (manual)      # visual stimulus trial
	-> psy.Session
	trial_idx       : int   # trial index within sessions
	---
	-> psy.Condition
	flip_times                  : mediumblob   # (s) row array of flip times
	last_flip_count             : int unsigned # the last flip number in this trial
	trial_ts=CURRENT_TIMESTAMP  : timestamp    # automatic
	"""
{% endhighlight %}


The corresponding MySQL command that DataJoint generates automatically upon the first use of the class is 
{% highlight sql %}

 CREATE TABLE `trial` (
  `animal_id` int(11) NOT NULL COMMENT 'id (internal to database)',
  `psy_id` smallint(5) unsigned NOT NULL COMMENT 'unique psy session number',
  `trial_idx` int(11) NOT NULL COMMENT 'trial index within sessions',
  `cond_idx` smallint(5) unsigned NOT NULL,
  `flip_times` mediumblob NOT NULL COMMENT '(s) row array of flip times',
  `last_flip_count` int(10) unsigned NOT NULL COMMENT 'the last flip number in this trial',
  `trial_ts` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT 'automatic',
  PRIMARY KEY (`animal_id`,`psy_id`,`trial_idx`),
  KEY `animal_id` (`animal_id`,`psy_id`,`cond_idx`),
  CONSTRAINT `trial_ibfk_1` FOREIGN KEY (`animal_id`, `psy_id`) 
      REFERENCES `session` (`animal_id`, `psy_id`) ON UPDATE CASCADE,
  CONSTRAINT `trial_ibfk_2` FOREIGN KEY (`animal_id`, `psy_id`, `cond_idx`) 
      REFERENCES `condition` (`animal_id`, `psy_id`, `cond_idx`) ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=latin1 COMMENT='visual stimulus trial' 
{% endhighlight %}

Luckily, you will never have to deal with it. 

## Simple code sharing
Since table definitions are included in simple Matlab/Python files, you only need to share the MATLAB files to replicate the same functionality somewhere else.

## Automated computations with referential integrity

DataJoint implements a standard process for populating computed data. This process uses referential constraints to enforce all data dependencies. This processes also uses transactional processing to ensure  that interrupted computations do not result in partially computed invalid results.

## Support for distributed/parallel processing
DataJoint provides a job reservations process to enable distributed processing on multiple processors or computers without conflict.

# What DataJoint is not

## DataJoint is not a general-purpose interface
DataJoint is designed for the sole purpose of providing a robust and intuitive data model for scientific data processing chains. As such it does not reproduce all the features and capabilities of SQL. To achieve its strict adherence to the relational data model, its expressive power and simplicity, DataJoint imposes some limitations and conventions. This is done intentionally to avoid dangerous usage of SQL and to make the data model logically sound.

Here are some examples of such conventions and limitations:

* DataJoint does not allow updating an individual attribute value in a given tuple (SQL's UPDATE command): all data manipulations are done by inserting or deleting whole tuples. This is done for good reason since referential constraints (foreign keys) only enforce data dependencies between tuples but not between individual attributes.

* DataJoint limits some operators to enforce clarity. For example, its projection operator does not allow projecting out any of the primary key attributes. This ensures that the result of the projection operator has the same cardinality as the original relation (the same number of rows). If the user really intends to produce a relation with a different primary key, she must explicitly declare such a relation in the form of a base relvar. Again, this is not a real limitation but a specific prescription of how things should be done in a uniform manner.

* In DataJoint, all foreign keys between tables are formed between identically named fields. This convention allows easy specification of functional dependencies and easy relational join operators. It also allows to replace the many forms of the join operator in other language with a single natural join operator. In a large schema, this convention may lead to long composite primary keys in tables that are low in the dependency hierarchy, but MySQL handles these with ease.  This convention is particularly important in DataJoint because it allows tables across the database or multiple databases to be logically linked without having to follow the path of intermediate dependencies. 


# Publications that used datajoint
1. Yatsenko D, Josic K, Ecker AS, Froudarakis E, Cotton RJ, and Tolias AS (2015). Improved estimation and interpretation of correlations in neural circuits. _PLoS Comput Biol_ 11(3): e1004083. doi:10.1371/journal.pcbi.1004083 [link](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1004083)
1. Erisken S, Vaiceliunaite A, Jurjut O, Fiorini M, Katzner S, and Busse, L (2014). Effects of Locomotion Extend throughout the Mouse Early Visual System. _Current Biology_, 24(24), 2899-2907.
1. Reimer J, Froudarakis E, Cadwell CR, Yatsenko D, Denfield GH, Tolias AS (2014). Pupil fluctuations track fast switching of cortical states during quiet wakefulness. _Neuron_, 84(2), 355-362.
1. Erisken S, Vaiceliunaite A, Jurjut O, Fiorini M, Katzner S, and Busse L (2014). Effects of Locomotion Extend throughout the Mouse Early Visual System. _Current Biology_, 24(24), 2899-2907.
1. Froudarakis E, Berens P, Ecker AS, Cotton RJ, Sinz FH, Yatsenko D, Saggau P, Bethge M, and Tolias AS. Population code in mouse V1 facilitates readout of natural scenes through increased sparseness. _Nature neuroscience_ (2014). [link](http://www.nature.com/neuro/journal/v17/n6/full/nn.3707.html) 
1. Ecker AS, Berens P, Cotton RJ, Subramaniyan M, Denfield GH, Cadwell CR, Smirnakis SM, Bethge M, and Tolias AS (2014): State dependence of noise correlations in macaque primary visual cortex. Neuron 82(1). [link](http://www.cell.com/neuron/abstract/S0896-6273(14)00104-4) [code](https://github.com/aecker/ecker2014) [data](http://bethgelab.org/files/ecker2014.zip) [pdf](http://toliaslab.files.wordpress.com/2011/06/ecker_2014_with_supplement.pdf)
1. Cotton RJ, Froudarakis E, Storer P, Saggau P, and Tolias AS (2013). Three-dimensional mapping of microcircuit correlation structure. _Frontiers in neural circuits_, 7. [pubmed 24133414](http://www.ncbi.nlm.nih.gov/pubmed/24133414)
1. Vaiceliunaite A, Erisken S, Franzen F, Katzner S, and Busse L (2013). Spatial integration in mouse primary visual cortex. _Journal of Neurophysiology_, 110(4), 964-972. [pubmed 23719206](http://www.ncbi.nlm.nih.gov/pubmed/23719206)

# License
DataJoint is free software under the [LGPL License](https://www.gnu.org/licenses/lgpl-2.1.html). In addition, we request that any use of DataJoint leading to a publication be acknowledged in the publication.

<!-- TODO: Include citation here! -->
