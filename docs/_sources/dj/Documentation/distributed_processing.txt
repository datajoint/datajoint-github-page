Distributed Processing
**********************

"Populated tables from multiple machines."

DataJoints computed tables support distributed processing. This allows you to start several processes on multiple computers at the same time to populate the table in parallel. This goes very well together with GNU parallel. This post quickly discusses how to setup distributed processing.

DataJoint implements job management for distributed computation. Job management is used when the `parpopulate` method is used to populate any table.

Upon the first invocation of `parpopulate` in a given schema, DataJoint will automatically create the job management table and its class `<package>.Jobs`.

The job management table contains an entry for each `makeTuples`/`_make_tuples` call that has been reserved for computed or that ended in error during the last execution (the error stack is saved). A successful completion of all population tasks will leave the job management table empty.


Executing multiple Make Tuples
==============================

Assume you have the following setup


.. code-block:: python

	import datajoint as dj

	myschema = dj.schema('mydatabasename', locals())

	@myschema
	class MyAnalysis(dj.Computed):

		def _make_tuples(self, key):
			# do something here



Then you can populate the with ``MyAnalysis().populate()``. If you additionally specify the keyword argument ``MyAnalysis().populate(reserve_jobs=True)`` then DataJoint will generate an extra table for job reservation to avoid that two results get computed twice. The job table is available from ``myschema.jobs``. If jobs exit successfully, then the corresponding job will be deleted form the table. If the job exits with an error, the table will contain information about the error.

Using GNU Parallel
==================

.. _here: http://unethicalblogger.com/2010/11/11/gnu-parallel-changed-my-life.html


Now you can start as many python scripts as you want that will populate the table. One very nice tool to do that is GNU parallel. A very nice post about it can be found here_. Here's an example to start 11 jobs. Just type the following in your shell

.. code-block:: bash

	printf 'python -c "MyAnalysis().populate(reserve_jobs=True)";\n%.0s' {1..11}| parallel -j 11

While the jobs are computing, you can check how many jobs are completed by using ``MyAnalysis().progress()`` in your python prompt.
