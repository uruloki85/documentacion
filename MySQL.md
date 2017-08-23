# MySQL

## Connect to a database
```
mysql -h host_name -P port_number -u username -p database_name
```

## Load a dump
Database must exist. Then run:
```
mysql -u user_name -p database_name < /path/to/file/name.sql
```
## Dump a database
```
mysqldump -u user_name -p database_name > /path/to/file/name.sql
```

## Managing MySQL server
* Status
```
service mysql status
```
* Start/stop
```
service mysql start/stop
```
