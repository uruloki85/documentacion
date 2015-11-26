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
* To determine PostgreSQL port:
```
sabela@pc:~$ sudo netstat -plunt | grep postgres
```
* To load a dump:
```
sabela@pc:~$ psql -h host -d database_name -U user -f file.sql -W
```
* To do a dump:
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

