---
layout: page
title: Installation
group: navigation
weight: 1
---

# Configure MySQL Server

##Does your institution already have a MySQL server?

Very likely your institution's IT department can administer a MySQL server and can give you access. Kindly request an account with necessary rights. 

You will need to create many databases.  To avoid conflicts with other users, ask the database administrator to give you full access to any database that begins with your username followed by an underscore. 

For example, if your username is `eleanor`, the command to run on the MySQL server is

{% highlight SQL %}
GRANT ALL PRIVILEGES ON `eleanor\_%`.* TO 'eleanor'@'%' 
{% endhighlight %}

##Installing  on your own

MySQL provides detailed information on installing and configuring a server on their [webpage](https://dev.mysql.com/doc/refman/en/installing.html).

We also recommend using [MariaDB](https://mariadb.com) rather than MySQL. 

##Linux
Under Debian/Ubuntu/Mint systems, the server is installed via

{% highlight bash %}
sudo apt-get install mysql-server
{% endhighlight %}

##Mac OS X
An all-in-one installation of the MySQL sever is provided by [MAMP](https://www.mamp.info/en/). 

If you are using a package manager such as [homebrew](http://brew.sh/), you can install MySQL or MariaDB. 
For example, this [blog](https://emson.co.uk/2012/01/installing-mariadb-using-homebrew/) describes installing MariaDB using brew.

##Windows
An all-in-one installation of the MySQL server is provided by [WAMP](http://www.wampserver.com).

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
* pandas
* networkx

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

Go to the directory, where you want to run scripts from. Open the python3 interpreter and import datajoint

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
    "database.host": "mysqlhost.university.edu",
    "database.password": "datajoint",
    "database.port": 3306,
    "loglevel": "DEBUG",
    "display.width": 14,
    "display.limit": 7,
    "safemode": true,
    "database.user": "myusername",
    "connection.init_function": null
}
{% endhighlight %}

and replace *USER* with your database user and *PASSWORD* with your database password. If you are not running mysql locally, you need to replace host as well. In most cases, this should be all you need to do. Now DataJoint will use these settings when you start it from the same directory. If you don't want to specify the password in the file, just delete the line. DataJoint will ask you for the password once it tries to connect to the database.

You can test the connection via
{% highlight python %}
import datajoint as dj
c = dj.conn()
{% endhighlight %}

This should show you 
{% highlight bash %}
Connected myuser@mysqlhost.university.edu:3306
{% endhighlight %}


<!-- Add the following lines to `~/.bashrc` (or `~/.zshrc` if you are fancy) or `~/.profile`:

{% highlight bash %}
EXPORT DJ_HOST = "mysqlhost.university.edu:3306"    
EXPORT DJ_USER = "myusername"
EXPORT DJ_PASS = "mypassword"
{% endhighlight %}

and replace the information in the string with your information. Note that the server port is appended to the host name, separated by a colon.
 -->



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

