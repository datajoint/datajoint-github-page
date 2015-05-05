---
layout: page
title: Installation
group: navigation
---
{% include JB/setup %}

# Prerequisites

## Mysql Server
**Do I need to install a MySQL server?**

Very likely your institution has a MySQL server running somewhere. Kindly request an account, a user name, and the necessary rights to setup databases. 

**Linux**
Under the widespread Debian/Ubuntu/Mint systems, the server is installed via

{% highlight bash %}
sudo apt-get install mysql-server
{% endhighlight %}

If you are not using one of those, we probably do not need to tell you how to install a mysql server. 

**Mac OS X**

For Mac OS X, you can install a local server via a [.dmg](http://dev.mysql.com/doc/mysql-macosx-excerpt/5.7/en/macosx-installation-pkg.html).

**Windows**

A description how to install MySQL on a local Windows machine can be found [here](http://dev.mysql.com/doc/refman/5.5/en/mysql-installer.html).

## Matlab or Python
**Matlab version**

* MATLAB R2011a or higher.  Earlier versions can be used with a few modifications. 
* [mYm library](https://github.com/datajoint/mym) used as the low-level MySQL API.  The original project is at [sourceforge](http://sourceforge.net/projects/mym/) and at [Matlab Central](http://www.mathworks.com/matlabcentral/linkexchange/links/1313-mysql-connector-mym).

**Python version**
DataJoint is written for python 3.4. For Mac OS X and Windows users, we recommend the [anaconda](http://continuum.io/downloads) distribution.  

Additionally, you will need the following libraries

* numpy
* pymysql
* matplotlib
* json

# Installing DataJoint

## Matlab

* Download the [latest version](https://github.com/datajoint/datajoint-matlab/archive/master.zip) and unzip it.
* Add the directory to your Matlab path.

## Python

Run

{% highlight bash %}
pip install git+https://github.com/datajoint/datajoint-python
{% endhighlight %}

from a command line window. 

Alternatively, you can download the [latest version](https://github.com/datajoint/datajoint-python/archive/master.zip), enter the directory and run

{% highlight bash %}
python setup.py install
{% endhighlight %}

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

### Providing the database information
Assign the connection information to environment variables `DJ_HOST`, `DJ_USER`, and `DJ_PASS` of you operating system. Environment variables are specified differently on different operating systems. 

**MacOS X**

Add the following lines to `~/.profile`:

{% highlight bash %}
EXPORT DJ_HOST = "mysqlhost.university.edu:3306"    
EXPORT DJ_USER = "myusername"
EXPORT DJ_PASS = "mypassword"
{% endhighlight %}

and replace the information in the string with your information. Note that the server port is appended to the host name, separated by a colon.

**Windows**

On Windows, the environment variables are set through the **Properties** dialog of the **My computer** icon. 

See 

* [http://support.microsoft.com/kb/310519](http://support.microsoft.com/kb/310519) (Windows XP)
* [http://www.itechtalk.com/thread3595.html](http://www.itechtalk.com/thread3595.html) (Windows 7).

**Linux**

Add the following lines to `~/.bashrc` or `~/.profile`:

{% highlight bash %}
EXPORT DJ_HOST = "mysqlhost.university.edu:3306"    
EXPORT DJ_USER = "myusername"
EXPORT DJ_PASS = "mypassword"
{% endhighlight %}

and replace the information in the string with your information. Note that the server port is appended to the host name, separated by a colon.




**Alternative**

Alternatively, you may set the environment variables directly from MATLAB. For example, you could include the following lines to your `startup.m`.

{% highlight matlab %}
setenv DJ_HOST mysqlhost.university.edu:3306    
setenv DJ_USER myusername
setenv DJ_PASS mypassword
{% endhighlight %}

**Remark**
For special technical reasons, sometimes users may want to execute an SQL query at the start of each connection. Use the optional environment variable `DJ_INIT` to specify the initial command. 

For example, setting the initial command to
```
setenv DJ_INIT 'SET SESSION sql_mode="TRADITIONAL"'
```
will set the subsequent connections to the [traditional SQL mode](http://dev.mysql.com/doc/refman/5.6/en/server-sql-mode.html). The initial command is used to modify session settings when they cannot be modified globally for all users using the server configuration file.

**Test**

From MATLAB, check that your environment variables are set, e.g.

{% highlight matlab %}
>> getenv DJ_HOST
{% endhighlight %}

To test your connection, type

{% highlight matlab %}
>> dj.conn
{% endhighlight %}

If you are familiar with SQL, you can execute SQL commands using the connection object returned by **dj.conn**:

{% highlight matlab %}
>> query(dj.conn, 'SHOW SCHEMAS')
{% endhighlight %}


## Python

Open the python3 interpreter and import datajoint

{% highlight python %}
import datajoint as dj
{% endhighlight %}

You will see the following output

{% highlight bash %}
Local config file dj_local_conf.json does not exist! Creating it.
{% endhighlight %}

Edit the file `dj_local_conf.json`

{% highlight json %}
{
  "database.user": "myusername",
  "database.port": 3306,
  "database.host": "mysqlhost.university.edu",
  "database.password": "mypassword",
  "connection.init_function": null,
  "config.varname": "DJ_LOCAL_CONF",
  "config.file": "dj_local_conf.json"
}
{% endhighlight %}

and replace *USER* with your database user and *PASSWORD* with your database password. If you are not running mysql locally, you need to replace host as well. In most cases, this should be all you need to do. Now DataJoint will use these settings when you start it from the same directory. 

You can test the connection via
{% highlight python %}
import datajoint as dj
c = dj.conn()
{% endhighlight %}

This should show you 
{% highlight bash %}
Connected myuser@mysqlhost.university.edu:3306
{% endhighlight %}
