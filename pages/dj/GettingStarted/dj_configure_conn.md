---
title: Configure Connection
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "How do I point datajoint at the correct server with my credentials?"
sidebar: dj_sidebar
permalink: dj_configure_conn.html
folder: dj
---



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


{% include links.html %}
