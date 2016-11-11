---
title: Matlab Install
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "How do I install the Matlab DataJoint library?"
sidebar: dj_sidebar
permalink: dj_matlab_install.html
folder: dj
---

## Matlab
**Matlab version**

* MATLAB R2011a or higher.  Earlier versions can be used with a few modifications.
* [mYm library](https://github.com/datajoint/mym) used as the low-level MySQL API.  The original project is at [sourceforge](http://sourceforge.net/projects/mym/) and at [Matlab Central](http://www.mathworks.com/matlabcentral/linkexchange/links/1313-mysql-connector-mym).


### Matlab

* Download the [latest version](https://github.com/datajoint/datajoint-matlab/archive/master.zip) and unzip it.
* Add the directory to your Matlab path.


# Setup the database connection

## Matlab

1. Download the [latest release of DataJoint](https://github.com/datajoint/datajoint-matlab/releases)
1. Edit [`startup.m`](http://www.mathworks.com/help/matlab/ref/startup.html) to add the datajoint directory to the search path and execute `<datajoint-path>/mym/mySetup.m`.

{% highlight matlab %}
% startup.m
addpath <datajoint path>
run <datajoint-path>/mym/mymSetup
{% endhighlight %}

At the MATLAB command prompt, test that DataJoint and mym are on your path:

{% highlight matlab %}
>> mym version
mYm version 2.6.8
>> which dj.conn
/<datajoint-path>/+dj/conn.m
{% endhighlight %}

To use DataJoint you need access to a MySQL server. In particular, you need:

1. the server host name, including the database server port
1. user name
1. password
1. the database name that will store your schema (not that a MySQL server can hold several databases)





{% include links.html %}
