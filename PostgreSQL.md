# Table of contents
1. [Essential commands](#essential-commands)
2. [Foreign Data Wrappers](#fdw)


# Essential commands <a name="essential-commands"></a>
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
or
```
sabela@pc:~$ createdb erapro -h localhost -p 4567 -U microaccounts
```
Use dropdb for dropping a database.
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
## Sending output to a file
```
psql -h host_name -p 5432 -d database_name -U username -W -c "COPY ( SELECT ... ) TO STDIN WITH CSV DELIMITER ';'" > file_name.csv
```

## Doing dumps
### Custom format
<code>pg_dump</code>
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

## Loading dumps
### Custom format
<code>pg_restore</code>
```
sabela@pc:~$ pg_restore -h localhost -p 5432 -d crg_erapro_dev -U microaccounts_dev -W -Fc -a < sql01_crg_erapro_schema.backup
```
- <code>-O/--no-owner</code>: Do not output commands to set ownership of objects to match the original database (mandatory if users are not the same).
- <code>--disable-triggers</code>: disable triggers while loading the dump.
- <code>-c/--clean</code>: clean (drop) database objects before recreating them.

### SQL format
<code>psql</code>
```
sabela@pc:~$ psql -h host -d database_name -U user -f file.sql -W
```

### CSV format
#### From CSV to DB
The following command populates a table with the content of the CSV file:
```
sabela@pc:~$ psql -h hostname -p port -d db_name -U username
db_name=> \copy table_name(column1,column2,...) from filename.csv with csv delimiter ';';
```
or
```
cat filename.csv" | psql -h hostname -p port -U username -c "copy table_name(column1,column2,...) from stdin using delimiters ';' csv" db_name
```
#### From DB to CSV
The following command copies the result of a SQL command into a CSV file:
```
psql -h localhost -p 5432 -U microaccounts_dev -c "copy (
select *
from beacon_data
where dataset_stable_id='XXXX') to stdin with csv delimiter ';'" ega_beacon_dev > file.csv
```
## Listing running queries
```sql
SELECT pid, datid, datname, state, xact_start, query 
FROM pg_stat_activity 
WHERE usename='microaccounts_dev' and state='active';
```
To kill a specific query:
```sql
SELECT pg_cancel_backend(pid);
```
## Databases size
To list all databases and their size:
```
sabela@pc:~$ psql -U postgres
postgres=# \l+
```
## To determine PostgreSQL port:
```
sabela@pc:~$ sudo netstat -plunt | grep postgres
```

## Installation path
* postgres.conf and pg_hba.conf: <code>/etc/postgresql/9.1/main/</code>
* Binaries: <code>/usr/lib/postgresql/9.3/</code>

# Foreign Data Wrappers <a name="fdw"></a>
## FDW to a Postgres DB
* Install the **postgres_fdw** extension using CREATE EXTENSION.
```sql
CREATE EXTENSION postgres_fdw;
```
* Create a foreign server object, using CREATE SERVER, to represent each remote database you want to connect to. Specify connection information, except user and password, as options of the server object.
```sql
CREATE SERVER erapro_server FOREIGN DATA WRAPPER postgres_fdw OPTIONS (host 'localhost', dbname 'erapro', port '4567');
```
* Create a user mapping, using CREATE USER MAPPING, for each database user you want to allow to access each foreign server. Specify the remote user name and password to use as user and password options of the user mapping.
```sql
CREATE USER MAPPING FOR local_username SERVER erapro_server OPTIONS (user 'remote_username', password 'remote_password');
```
* Create a foreign table, using CREATE FOREIGN TABLE, for each remote table you want to access. The columns of the foreign table must match the referenced remote table. You can, however, use table and/or column names different from the remote table's, if you specify the correct remote names as options of the foreign table object.
```sql
CREATE FOREIGN TABLE fdw_erapro.analysis_ega_dataset
(
  analysis_id character varying(15) NOT NULL,
  ega_dataset_id character varying(15) NOT NULL
)
SERVER erapro_server OPTIONS (schema_name 'public');
```
* Grant privileges
```sql
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO microaccounts;
```

## FDW to a Mysql DB
* Install the extension following the steps: https://github.com/EnterpriseDB/mysql_fdw
* Add the extension to Postgres:
```sql
CREATE EXTENSION mysql_fdw;
```
* Create the server:
```sql
CREATE SERVER mysql_server FOREIGN DATA WRAPPER mysql_fdw OPTIONS (host 'localhost', port '3306');
```
* Create a user mapping:
```sql
CREATE USER MAPPING FOR local_username SERVER mysql_server OPTIONS (username 'remote_username', password 'remote_password');
```
* Create foreign tables:
```sql
CREATE FOREIGN TABLE fdw_audit.audit_file (
  file_id serial,
  file_name varchar(1000) NOT NULL
)
SERVER mysql_server OPTIONS (dbname 'remote_mysql_database_name', table_name 'remote_table_name');
```
# Postgres SQL
## Pattern matching
```sql
SELECT array_length(regexp_matches(d.stable_id,'^EGAD00001\d+'), 1) > 0 FROM dataset d;
```
Returns true or false if the stable id matches or not the pattern.

## Filter by date
```sql
select s.*
from sample_table s
where s.edited_at::date = '2016-10-25';
```

## Using XPATH and unnest()
```sql
SELECT dt.id AS sample_id,
    dt.ega_stable_id AS sample_stable_id,
    (xpath('//TAG/text()'::text, dt._xml))[1]::character varying AS tag,
    (xpath('//VALUE/text()'::text, dt._xml))[1]::character varying AS value
FROM ( 
	SELECT dt_1.id,
		dt_1.ega_stable_id,
		dt_1.ebi_xml,
		unnest(xpath('/SAMPLE_SET/SAMPLE[1]/SAMPLE_ATTRIBUTES/SAMPLE_ATTRIBUTE'::text, dt_1.ebi_xml)) AS _xml
	FROM sample_table dt_1
) dt;

select unnested.ega_dac_id, 
	(xpath('@email'::text, unnested.dac_xml))[1]::character varying AS email,
	(xpath('@name'::text, unnested.dac_xml))[1]::character varying as name,
	(xpath('@organisation'::text, unnested.dac_xml))[1]::character varying as organisation,
	(xpath('@telephone_number'::text, unnested.dac_xml))[1]::character varying as telephone_number,
	(xpath('@main_contact'::text, unnested.dac_xml))[1]::character varying as main_contact
from (
	select d.ega_dac_id,
		unnest(xpath('/DAC_SET/DAC[1]/CONTACTS/CONTACT', d.ega_dac_xml)) AS dac_xml
	from stg_erapro.ega_dac d 
)unnested;
```
## Using loops
Useful when the query lasts for very long and we want to see how the execution is going.

NOTE: Always use ORDER BY when using limit. Otherwise, the result may be unpredicable!
```sql
CREATE OR REPLACE FUNCTION tmp.insert_rows()
  RETURNS integer AS
$BODY$
DECLARE
	_limit integer;
	_offset integer;
	_total_rows integer;
BEGIN
_limit=5000;
_offset=2000;
_total_runs=0;

select count(*) into _total_rows
from table1;

RAISE NOTICE '_total_rows=%', _total_rows;

LOOP 
EXIT WHEN _offset > _total_rows ; 
	RAISE NOTICE 'Inside loop. _offset=%', _offset;
	
	INSERT INTO table2(column1, column2, ...)
	SELECT DISTINCT column1, ...
	FROM (
		SELECT t.column1, ...
		FROM table1 t
		order by t.column1
		limit _limit offset _offset
	)subq;
	
	_offset=_offset+_limit;	
END LOOP;

RAISE NOTICE 'End loop. _offset=%', _offset;

RETURN _offset;

END;
$BODY$
LANGUAGE plpgsql;
```
