PostgreSQL
--------------

* Login as **postgres** user (default administrator):
```
sabela@pc:~$ sudo -i -u postgres
```
Use <code>psql</code> to get a Postgres prompt.
Use <code>\q</code> to exit this promp.

* Create a new role:
```
postgres@pc:~$ createuser --interactive
```
This user needs to be a Linux user, too. If it doesn't exist, create it using:
```
sabela@pc:~$ sudo adduser microaccounts
```
* Create a database
```
postgres@pc:~$ createdb erapro
```
* Connect to DB:
```
postgres@pc:~$ psql erapro
```
Grant permissions to this user (run as postgres user):
```
erapro=> grant all privileges on database erapro to microaccounts;
erapro=> grant all privileges on all tables in schema public to microaccounts;
```
* To do login in the postgres server:
```
sabela@pc:~$ sudo -i -u microaccounts
```
* Access to database:
```
sabela@pc:~$ psql -d erapro -U microaccount
```
###To determine PostgreSQL port:
```
sabela@pc:~$ sudo netstat -plunt | grep postgres
```
###To load a dump in a SQL format
```
sabela@pc:~$ psql -h host -d database_name -U user -f file.sql -W
```
###To do a dump
```
sabela@pc:~$ pg_dump -h localhost -p 5432 -d crg_erapro -U microaccounts -W -Fc -s > sql01_crg_erapro_schema.backup
```
- <code>-h/--host</code>: host name of the machine where the postgres server is running.
- <code>-p/--port</code>: postgres server port.
- <code>-d/--dbname</code>: name of the database to connect to.
- <code>-U/--username</code>: username to connect as.
- <code>-W/--password</code>: will prompt for the password.
- <code>-Fc/--format=c</code>: file format will be custom (c).
- <code>-s/--schema-only</code>: dump only the schema.
- <code>-a/--data-only</code>: dump only the data.

###To load a dump
```
sabela@pc:~$ pg_restore -h localhost -p 5432 -d crg_erapro_dev -U microaccounts_dev -W -Fc -a < sql01_crg_erapro_schema.backup
```
- <code>-O/--no-owner</code>: Do not output commands to set ownership of objects to match the original database (mandatory if users are not the same).

###Access tables in another Postgres DB
* Install the **postgres_fdw** extension using CREATE EXTENSION.
```
CREATE EXTENSION postgres_fdw;
```
* Create a foreign server object, using CREATE SERVER, to represent each remote database you want to connect to. Specify connection information, except user and password, as options of the server object.
```
CREATE SERVER erapro_server FOREIGN DATA WRAPPER postgres_fdw OPTIONS (host 'localhost', dbname 'erapro', port '4567');
```
* Create a user mapping, using CREATE USER MAPPING, for each database user you want to allow to access each foreign server. Specify the remote user name and password to use as user and password options of the user mapping.
```
CREATE USER MAPPING FOR local_username SERVER erapro_server OPTIONS (user 'remote_username', password 'remote_password');
```
* Create a foreign table, using CREATE FOREIGN TABLE, for each remote table you want to access. The columns of the foreign table must match the referenced remote table. You can, however, use table and/or column names different from the remote table's, if you specify the correct remote names as options of the foreign table object.
```
CREATE FOREIGN TABLE fdw_erapro.analysis_ega_dataset
(
  analysis_id character varying(15) NOT NULL,
  ega_dataset_id character varying(15) NOT NULL
)
SERVER erapro_server OPTIONS (schema_name 'public');
```
* Grant privileges
```
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO microaccounts;
```
### Installation path
* postgres.conf and pg_hba.conf: <code>/etc/postgresql/9.1/main/</code>
* Binaries: <code>/usr/lib/postgresql/9.3/</code>

###Access tables in a Mysql DB
* Install the extension following the steps: https://github.com/EnterpriseDB/mysql_fdw
* Add the extension to Postgres:
```
CREATE EXTENSION mysql_fdw;
```
* Create the server:
```
CREATE SERVER mysql_server FOREIGN DATA WRAPPER mysql_fdw OPTIONS (host 'localhost', port '3306');
```
* Create a user mapping:
```
CREATE USER MAPPING FOR local_username SERVER mysql_server OPTIONS (username 'remote_username', password 'remote_password');
```
* Create foreign tables:
```
CREATE FOREIGN TABLE fdw_audit.audit_file (
  file_id serial,
  file_name varchar(1000) NOT NULL
)
SERVER mysql_server OPTIONS (dbname 'remote_mysql_database_name', table_name 'remote_table_name');
```
