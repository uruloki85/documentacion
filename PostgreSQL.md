# Table of contents
1. [Essential commands](#essential-commands)
	1. [Sending output to a file](#sending-output-to-file)
	1. [Doing dumps](#doing-dumps)
		1. [Custom format](#doing-custom-format)
	1. [Loading dumps](#loading-dumps)
		1. [Custom format](#loading-custom-format)
		1. [SQL format](#sql-format)
		1. [CSV format](#csv-format)
			1. [From CSV to DB](#csv-to-db)
			1. [From DB to CSV](#db-to-csv)
	1. [Listing running queries](#running-queries)
	1. [Databases size](#db-size)
	1. [To determine PostgreSQL port](#postgres-port)
	1. [Installation path](#installation-path)
	1. [Check server status](#server-status)
1. [Foreign Data Wrappers](#fdw)
	1. [FDW to a Postgres DB](#fdw-to-postgres)
	1. [FDW to a Mysql DB](#fdw-to-mysql)
1. [Postgres SQL](#postgres-sql)
	1. [Pattern matching](#pattern-matching)
		1. [Replacement](#pattern-replacement)
	1. [Filter by date](#filter-by-date)
	1. [Using XPATH and unnest()](#xpath-unnest)
	1. [Sorting in combination with unnest](#sorting-with-unnest)
	1. [Using loops](#using-loops)
	1. [Check empty string](#check-emtpy-string)

<a name="essential-commands"></a>
# Essential commands
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
<a name="sending-output-to-file"></a>
## Sending output to a file
```
psql -h host_name -p 5432 -d database_name -U username -W -c "COPY ( SELECT ... ) TO STDIN WITH CSV DELIMITER ';'" > file_name.csv
```
<a name="doing-dumps"></a>
## Doing dumps
<a name="doing-custom-format"></a>
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
- <code>-n/--schema</code>: dump only this schema. Multiple schemas can be selected by writing multiple <code>-n</code> switches.

<a name="loading-dumps"></a>
## Loading dumps
<a name="loading-custom-format"></a>
### Custom format
<code>pg_restore</code>
```
sabela@pc:~$ pg_restore -h localhost -p 5432 -d crg_erapro_dev -U microaccounts_dev -W -Fc -a < sql01_crg_erapro_schema.backup
```
- <code>-O/--no-owner</code>: Do not output commands to set ownership of objects to match the original database (mandatory if users are not the same).
- <code>--disable-triggers</code>: disable triggers while loading the dump.
- <code>-c/--clean</code>: clean (drop) database objects before recreating them.

<a name="sql-format"></a>
### SQL format
<code>psql</code>
```
sabela@pc:~$ psql -h host -d database_name -U user -f file.sql -W
```
<a name="csv-format"></a>
### CSV format
<a name="csv-to-db"></a>
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
<a name="db-to-csv"></a>
#### From DB to CSV
The following command copies the result of a SQL command into a CSV file:
```
psql -h localhost -p 5432 -U microaccounts_dev -c "copy (
select *
from beacon_data
where dataset_stable_id='XXXX') to stdin with csv delimiter ';'" ega_beacon_dev > file.csv
```
<a name="running-queries"></a>
## Listing running queries
```sql
SELECT pid, datid, datname, state, xact_start, now()-xact_start as duration, query 
FROM pg_stat_activity 
WHERE usename='microaccounts_dev' and state='active';
```
To kill a specific query:
```sql
SELECT pg_cancel_backend(pid);
```
<a name="db-size"></a>
## Databases size
To list all databases and their size:
```
sabela@pc:~$ psql -U postgres
postgres=# \l+
```
<a name="postgres-port"></a>
## To determine PostgreSQL port
```
sabela@pc:~$ sudo netstat -plunt | grep postgres
```
<a name="installation-path"></a>
## Installation path
* postgres.conf and pg_hba.conf: <code>/etc/postgresql/9.1/main/</code>
* Binaries: <code>/usr/lib/postgresql/9.3/</code>
<a name="server-status"></a>
## Check server status
```
service postgresql status
```
<a name="fdw"></a>
# Foreign Data Wrappers 
<a name="fdw-to-postgres"></a>
## FDW to a Postgres DB
* Install the **postgres_fdw** extension using CREATE EXTENSION.
```sql
CREATE EXTENSION postgres_fdw;
```
* It might be required to grant privileges:
```
GRANT USAGE ON FOREIGN DATA WRAPPER postgres_fdw TO microaccounts_dev;
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
<a name="fdw-to-mysql"></a>
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
<a name="postgres-sql"></a>
# Postgres SQL
<a name="pattern-matching"></a>
## Pattern matching
```sql
SELECT array_length(regexp_matches(d.stable_id,'^EGAD00001\d+'), 1) > 0 FROM dataset d;
```
Returns true or false if the stable id matches or not the pattern.
<a name="pattern-replacement"></a>
### Replacement
```sql
select dat.stable_id, 
	array_to_json(
	ARRAY(
		SELECT DISTINCT *
		from
		unnest(
			string_to_array( 
				trim(both ',' from 
					regexp_replace(
						trim(both ' ' from regexp_replace(dat.technology, '\s*(,|;)\s*', ',', 'g'))
					, ',+', ',', 'g')
				)
			, ',')
		) --closing unnest
	) --closing array
) --closing array_to_json
as technology_json
from stg_ega_website_prod.dataset dat
```
1. Replaces , and ; with , for all occurences (g option).
1. Replaces multiple , with just one.
1. Removes leading and trailing ,
1. Converts to an array
1. Unnests the array
1. Removes duplicates
1. Converts to array
1. Converts to json


<a name="filter-by-date"></a>
## Filter by date
```sql
select s.*
from sample_table s
where s.edited_at::date = '2016-10-25';
```
<a name="xpath-unnest"></a>
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
<a name="sorting-with-unnest"></a>
## Sorting in combination with unnest
```sql
select 
	dt.ega_stable_id, 
	array_agg(distinct dt.email order by dt.email) as erapro_emails, 
	array_agg(distinct dt.names order by dt.names) as erapro_names,
	array_agg(distinct audit.email order by audit.email) as audit_emails,
	array_agg(distinct audit.name order by audit.name) as audit_names,
	-- including EGAPRO info
	array_agg(distinct cont.email order by cont.email) AS egapro_emails,
	array_agg(distinct cont.name order by cont.name) AS egapro_names
from (
	select distinct dt.id,
		dt.ega_stable_id, 
		unnest(xpath('/DAC_SET/DAC[1]/CONTACTS/CONTACT/@email'::text, dt.ebi_xml))::text as email,
		unnest(xpath('/DAC_SET/DAC[1]/CONTACTS/CONTACT/@name'::text, dt.ebi_xml))::text as names
	from fdw_egapro.dac_table dt
	order by dt.ega_stable_id, email
) dt
inner join (
	select distinct a_dac.stable_id,
		a_c.email, 
		concat(a_c.first_name,' ', a_c.last_name) as name,
		a_c.telephone_number as phone, 
		a_c.contact_id,
		a_dc.primary_contact
	from fdw_audit.dac a_dac
	inner join fdw_audit.dac_contact a_dc on a_dc.dac_id=a_dac.dac_id
	inner join fdw_audit.contact a_c on a_c.contact_id=a_dc.contact_id
	order by a_dac.stable_id, a_c.email
) audit on audit.stable_id=dt.ega_stable_id
-- including EGAPRO info (skipping disabled rows!!)
inner join fdw_egapro.dac_contact_table dac_con ON dac_con.dac_id=dt.id and dac_con.disabled_flag=false
inner join fdw_egapro.contact_table cont ON cont.id=dac_con.contact_id
group by dt.ega_stable_id
order by dt.ega_stable_id;
```
<a name="using-loops"></a>
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
<a name="check-emtpy-string"></a>
## Check empty string
```sql
CREATE OR REPLACE FUNCTION tool.check_not_empty(_value text)
RETURNS boolean AS
----------------------------------------------------------------
-- Returns TRUE if the string is not null and not empty ('').
-- FALSE otherwise.
--select tool.check_not_empty(''); --> false
--select tool.check_not_empty(null); --> false
--select tool.check_not_empty('hola'); --> true
--select tool.check_not_empty(' '); --> false
--select tool.check_not_empty('       '); --> false
--select tool.check_not_empty('    h   '); --> true
----------------------------------------------------------------
$BODY$
BEGIN

IF (trim(both from _value) = '') IS FALSE THEN
	return TRUE;
ELSE
	return FALSE;
END IF;

END
$BODY$
LANGUAGE plpgsql;
```
