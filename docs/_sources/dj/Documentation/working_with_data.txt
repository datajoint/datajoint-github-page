Working with Data
*****************

Inserting data
==============

Inserting in Matlab
-------------------
One can insert tuples into tables from MATLAB scripts using the `insert` command of a :ref:`base relvar <Representing Table Relationships>`.

For example, insert information about an animal from the MATLAB command line.

.. code-block:: matlab

  insert(common.Animal, struct(...
      'animal_id', 13, ...
      'date_of_birth', '2012-07-01', ...
      'animal_notes', 'test dummy'))

or insert multiple tuples


.. code-block:: matlab

  insert(common.Animal, cell2struct({
           13        '2012-07-01'      'M'   'test dummy'
           14        '2012-07-02'      'F'   'another test'
           15        '2012-08-01'      'M'   ''
  }',  {'animal_id'  'date_of_birth'  'sex'  'animal_notes'})


For imported and computed tables, one should only insert data from the `makeTuples` callback as part of DataJoint's standard process for populating computed data.

See also

.. code-block:: matlab

  help('dj.BaseRelvar/insert')


Inserting in Python
-------------------

The equivalent calls in Python would be

.. code-block:: python

  common.Animal.insert1(
  		dict(animal_id=13, date_of_birth='2012-07-01', sex='M', animal_notes='test dummy'))


or insert multiple tuples


.. code-block:: python

  common.Animal.insert([
  	dict(animal_id=13, date_of_birth='2012-07-01', sex='M', animal_notes='test dummy')),
  	dict(animal_id=14, date_of_birth='2012-07-02', sex='F', animal_notes='another test')),
  	dict(animal_id=15, date_of_birth='2012-08-01', sex='M', animal_notes=''))
  	])



Fetching Data
=============

As discussed previously, :ref:`base relvars <Representing Table Relationships>` and derived relvars only represent the data in the database but do not themselves contain any of it. The `fetch` operators retrieve the data represented by relvars into the Matlab/Python workspace.

The fetch operators always perform a relational [projection]( 2015-05-05-projection) and accept the same arguments as the projection of the corresponding relvar.

Fetching in Matlab
------------------
For more details, see

.. code-block:: matlab

  >> doc dj.Relvar/pro
  >> doc dj.Relvar/fetch
  >> doc dj.Relvar/fetch1
  >> doc dj.Relvar/fetchn

Fetching in Python
------------------
DataJoint Relations in Python provide the member functions `fetch1` and `fetch`. These return numpy structured arrays by default, but can also return a list of dictionaries. Fetching into pandas DataFrames is planned, but not yet implemented.

Deleting data
=============
Deleting a set of tuples from a table will also recursively delete all corresponding tuples in all its :ref:`dependent tables<Foreign keys>`.

To delete a subset of tuples, any kind of restriction can be applied to the base relvar before deleting. However, other operators such as ``join`` or ``projection`` will produce a relation that cannot be deleted.


Matlab Delete Examples
----------------------
To delete a selection of tuples from a [base relvar]( 2015-05-05-baserelvars), use the `del` command.

.. code-block:: matlab

  % deleting an entire table
  del(tp.Segmentation)

  % deleting a subset of a table
  del(tp.Segmentation & (common.TpSession & 'session_date >= "2013-01-01"'))

The second statement deletes all tuples from `tp.Segmentation` that match tuples from `common.TpSession` with `session_date` greater than `2013-01-01`. Or, in English, ``delete all segmentations from two-photon sessions acquired since Jan 1, 2013``.

Again, the delete will cascade to all the dependent tables.

A summary of data to be deleted is displayed to the user before the delete is executed. Once committed, the deletion is permanent and cannot be undone.

When precautions are unnecessary, a faster way to delete a selection of tuples from a table is the method `delQuick`. It is non-interactive and it does not cascade the delete down to dependent tables.

See also

.. code-block:: matlab

  help dj.BaseRelvar/del
  help dj.BaseRelvar/delQuick


Python Delete Examples

To delete a selection of tuples from a [base relvar]( 2015-05-05-baserelvars), you can use the objects function `delete`. For example, if `segm = tp.Segmentation()`, then

.. code-block:: python

  # deleting an entire table is carried out via the command
	segm.delete()

  # deleting a subset of a table (`sess = common.TpSession()`)
	(segm & (sess & 'session_date >= "2013-01-01"')).delete()

The second statement deletes all tuples from `segm` that match tuples from `sess` with `session_date` greater than `2013-01-01`. Or, in English, ``delete all segmentations from two-photon sessions acquired since Jan 1, 2013``.

Again, the delete will cascade to all the dependent tables.

**Warning:** In contrast to Matlab, Python will only display a summary or ask you to confirm the delete if you set `"safemode": true` in `dj_local_conf.json`.
