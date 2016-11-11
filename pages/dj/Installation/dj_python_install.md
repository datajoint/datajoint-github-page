---
title: Python Install
keywords: dev
last_updated: July 3, 2016
tags: null
summary: "How do I install the Python DataJoint library?"
sidebar: dj_sidebar
permalink: dj_python_install.html
folder: dj
---
**Python version**
DataJoint is written for python 3.4. For Mac OS X and Windows users, we recommend the [anaconda](http://continuum.io/downloads) distribution.  

Additionally, you will need the following libraries

* numpy
* pymysql
* matplotlib
* json
* pandas
* networkx

### Python

Run

{% highlight bash %}
pip install git+https://github.com/datajoint/datajoint-python
{% endhighlight %}

from a command line window.

Alternatively, you can download the [latest version](https://github.com/datajoint/datajoint-python/archive/master.zip), enter the directory and run

{% highlight bash %}
python setup.py install
{% endhighlight %}

{% include links.html %}
