Prerequisites
*************



Configuring MySQL Server
========================

Using an institution's server
-----------------------------

Does your institution already have a MySQL server?

Very likely your institution's IT department can administer a MySQL server and can give you access. Kindly request an account with necessary rights.

You will need to create many databases.  To avoid conflicts with other users, ask the database administrator to give you full access to any database that begins with your username followed by an underscore.

For example, if your username is `eleanor`, the command to run on the MySQL server is

.. code-block:: sql

  GRANT ALL PRIVILEGES ON `eleanor_%`.* TO 'eleanor'@'%'


Installing mysql on your own computer
-------------------------------------

.. _webpage: https://dev.mysql.com/doc/refman/en/installing.html
.. _MariaDB: https://mariadb.com


MySQL provides detailed information on installing and configuring a server on their webpage_.

We also recommend using MariaDB_ rather than MySQL.

Linux
+++++

Under Debian/Ubuntu/Mint systems, the server is installed via

.. code-block:: bash

  sudo apt-get install mysql-server


Mac OS X
++++++++

.. _MAMP: https://www.mamp.info/en/
.. _blog: https://emson.co.uk/2012/01/installing-mariadb-using-homebrew/


An all-in-one installation of the MySQL sever is provided by MAMP_.

If you are using a package manager such as homebrew, you can install MySQL or MariaDB.
For example, this blog_ describes installing MariaDB using brew.

Windows
+++++++

.. _WAMP: http://www.wampserver.com

An all-in-one installation of the MySQL server is provided by WAMP_.
