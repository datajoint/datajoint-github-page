---
layout: post
title: 'Distributed processing with datajoint and GNU parallel'
tags: ['blog','tutorial', 'python','help']
---

DataJoints computed tables support distributed processing. This allows you to start several processes on multiple computers at the same time to populate the table in parallel. This goes very well together with GNU parallel. This post quickly discusses how to setup distributed processing.


Assume you have the following setup


{% highlight python %}

import datajoint as dj

myschema = dj.schema('mydatabasename', locals())

@myschema
class MyAnalysis(dj.Computed):

	def _make_tuples(self, key):
		# do something here

{% endhighlight %}

Then you can populate the with ```MyAnalysis().populate()```. If you additionally specify the keyword argument ```MyAnalysis().populate(reserve_jobs=True)``` then DataJoint will generate an extra table for job reservation to avoid that two results get computed twice. The job table is available from ```myschema.jobs```. If jobs exit successfully, then the corresponding job will be deleted form the table. If the job exits with an error, the table will contain information about the error. 


Now you can start as many python scripts as you want that will populate the table. One very nice tool to do that is GNU parallel. A very nice post about it can be found [here](http://unethicalblogger.com/2010/11/11/gnu-parallel-changed-my-life.html). Here's an example to start 11 jobs

```printf 'python analyses.py;\n%.0s' {1..11}| parallel -j 11```

While the jobs are computing, you can check how many jobs are completed by ```MyAnalysis().progress()```.
