---
title: Prerequisites  
keywords: documentation theme, jekyll, technical writers, help authoring tools, hat replacements
last_updated: July 3, 2016
tags: null
#summary: "I have used this theme for projects that I've worked on as a professional technical writer."
sidebar: dj_sidebar
permalink: dj_prereqs.html
folder: dj/GettingStarted
---


## Configure MySQL Server

### Using an institution's server

Does your institution already have a MySQL server?

Very likely your institution's IT department can administer a MySQL server and can give you access. Kindly request an account with necessary rights.

You will need to create many databases.  To avoid conflicts with other users, ask the database administrator to give you full access to any database that begins with your username followed by an underscore.

For example, if your username is `eleanor`, the command to run on the MySQL server is

{% highlight SQL %}
GRANT ALL PRIVILEGES ON `eleanor\_%`.* TO 'eleanor'@'%'
{% endhighlight %}

### Installing mysql on your own

MySQL provides detailed information on installing and configuring a server on their [webpage](https://dev.mysql.com/doc/refman/en/installing.html).

We also recommend using [MariaDB](https://mariadb.com) rather than MySQL.

#### Linux
Under Debian/Ubuntu/Mint systems, the server is installed via

{% highlight bash %}
sudo apt-get install mysql-server
{% endhighlight %}

#### Mac OS X
An all-in-one installation of the MySQL sever is provided by [MAMP](https://www.mamp.info/en/).

If you are using a package manager such as [homebrew](http://brew.sh/), you can install MySQL or MariaDB.
For example, this [blog](https://emson.co.uk/2012/01/installing-mariadb-using-homebrew/) describes installing MariaDB using brew.

#### Windows
An all-in-one installation of the MySQL server is provided by [WAMP](http://www.wampserver.com).







{% include links.html %}
