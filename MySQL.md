# MySQL

### Load a dump
Database must exist. Then run:
```
mysql -u user_name -p database_name < /path/to/file/name.sql
```
### Dump a database
```
mysqldump -u user_name -p database_name > /path/to/file/name.sql
```

### Managing MySQL server
* Status
```
service mysql status
```
* Start/stop
```
service mysql start/stop
```
