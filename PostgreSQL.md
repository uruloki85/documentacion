PostgreSQL
--------------

* Login as **postgres** user (default administrator):
```
sabela@pc:~$ sudo -u postgres psql
```
* Use <code>psql</code> to get a Postgres prompt.
* Use <code>\q</code> to exit this promp.
* Create a new role:
```
createuser --interactive
```
This user needs to be a Linux user, too. If it doesn't exist, create it using:
```
sabela@pc:~$ sudo adduser microaccounts
```
* Create a database
```
createdb erapro
```
* Connect to DB:
```
psql erapro
```
Grant permissions to this user (run as postgres user):
```
grant all privileges on database erapro to microaccounts;
grant all privileges on all tables in schema public to microaccounts;
```
* To do login:
```
sabela@pc:~$ sudo -i -u microaccounts
```
* Access to database:
```
microaccounts@pc:~$ psql -d erapro -U microaccount
```
* To determine PostgreSQL port:
```
sabela@pc:~$ sudo netstat -plunt | grep postgres
```
