Entitity Relationship Diagram
*****************************


"Plot the relationship between tables in a schema"

ERD in Matlab
=============

The entity relation ship diagram displays the tables in the schema and the foreign key dependencies between them. To view the entity relationship diagram of the entire DataJoint schema called `<package>`, type


.. code-block:: matlab

  >> erd <package>

To view the diagram of the immediate neighborhood of a single [base relvar](base relvars), type


.. code-block:: matlab

  >> erd <package>.<ClassName>

.. role:: blue
.. role:: green
.. role:: red
.. role:: grey

Since the ERD is a directed acyclic graph, all dependencies point upward and arrowheads are omitted. The font color indicates the tier of each table :grey:`lookup`, :green:`manual`, :blue:`imported`, and :red:`computed`. And the asterisk * indicates a subtable. Solid edges indicate hierarchical dependencies and dashed edges indicate non-hierarchical dependencies.

ERD Examples
============
TODO some good examples

Review ERDs for the schemas in the sessions repository https://github.com/atlab/sessions
