---
layout: page
title: Getting Started
group: navigation
---
{% include JB/setup %}

# Before you start

Make sure that you corretly [installed DataJoint](/installation.html).

# Define how you data is organized: Create a Schema

## What is a schema?
A DataJoint project is organized as a dedicated _schema_: a collection of logically related tables hosted on the database server. The word 'schema' is synonymous with 'database' in this context.

On the Matlab side, each schema has its own _namespace_ or _package_. Here is the [MathWorks page about packages](http://www.mathworks.com/help/matlab/matlab_oop/scoping-classes-with-packages.html). In Python each schema has its own _module_ or _package_.

A _DataJoint schema_ is the pairing of a MySQL schema and Matlab/Python package. Therefore, by _schema_ we will often mean such a pairing and not just the database.

DataJoint easily supports projects that span multiple schemas. For example, it is very convenient to set up a common schema for the acquisition and pre-processing of data while data analysis may be performed in separate schemas.

## Create a DataJoint schema

Now let's set up a Matlab package to work with your schema. For example, imagine that we have just created a schema (database) called 'two_photon' and we now want to create its objects in Matlab or Python.

### Matlab
