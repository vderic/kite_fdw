Kite Foreign Data Wrapper for PostgreSQL
=========================================

This PostgreSQL extension implements a Foreign Data Wrapper (FDW) for
Kite.

Please note that this version of kite_fdw works with PostgreSQL and EDB
Postgres Advanced Server 10, 11, 12, 13, 14, and 15.

Installation
------------

To compile the Kite foreign data wrapper,

1. To build on POSIX-compliant systems you need to ensure the
   `pg_config` executable is in your path when you run `make`. This
   executable is typically in your PostgreSQL installation's `bin`
   directory. For example:

    ```
    $ export PATH=/usr/local/pgsql/bin/:$PATH
    ```

2. Compile the code using make.

    ```
    $ make USE_PGXS=1
    ```

3.  Finally install the foreign data wrapper.

    ```
    $ make USE_PGXS=1 install
    ```

If you run into any issues, please [let us know][2].


Usage
-----

The following parameters can be set on a MySQL foreign server object:

  * `host`: List of addresses or hostname:port of the Kite servers. Defaults to
    `127.0.0.1:7878,127.0.0.1:7879`
  * `fragcnt`: The number of fragment per query
  * `fetch_size`: This option specifies the number of rows kite_fdw should
    get in each fetch operation. It can be specified for a foreign table or
    a foreign server. The option specified on a table overrides an option
    specified for the server. The default is `100`.

The following parameters can be set on a Kite foreign table object:

  * `dbname`: Name of the MySQL database to query. This is a mandatory
    option.
  * `table_name`: Name of the MySQL table, default is the same as
    foreign table.
  * `max_blob_size`: Max blob size to read without truncation.
  * `fetch_size`: Same as `fetch_size` parameter for foreign server.

The following parameters need to supplied while creating user mapping.

  * `username`: Username to use when connecting to MySQL.
  * `password`: Password to authenticate to the MySQL server with.

Examples
--------

```sql
-- load extension first time after install
CREATE EXTENSION kite_fdw;

-- create server object
CREATE SERVER kite_server
	FOREIGN DATA WRAPPER kite_fdw
	OPTIONS (host '127.0.0.1:7878', dbname 'pgsql', fragcnt '4');

-- create user mapping
CREATE USER MAPPING FOR pgsql
	SERVER kite_server
	OPTIONS (username 'foo', password 'bar');

-- create foreign table
CREATE FOREIGN TABLE warehouse
	(
		warehouse_id int,
		warehouse_name text,
		warehouse_created timestamp
	)
	SERVER kite_server
	OPTIONS (schema_name 'public', table_name 'warehouse*');

-- select from table
SELECT * FROM warehouse ORDER BY 1;

warehouse_id | warehouse_name | warehouse_created
-------------+----------------+-------------------
           1 | UPS            | 10-JUL-20 00:00:00
           2 | TV             | 10-JUL-20 00:00:00
           3 | Table          | 10-JUL-20 00:00:00


Contributing
------------
If you experience any bug and have a fix for that, or have a new idea,
create a ticket on github page.

Support
-------
This project will be modified to maintain compatibility with new
PostgreSQL and EDB Postgres Advanced Server releases.

If you require commercial support, please contact the Kite sales
team, or check whether your existing PostgreSQL support provider can
also support kite_fdw.
