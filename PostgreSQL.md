PostgreSQL
--------------

* Login as **postgres** user (default administrator):
```
sudo -u postgres psql
```
* Use <code>psql</code> to get a Postgres prompt.
* Use <code>\q</code> to exit this promp.
* Create a new role:
```
createuser --interactive
```
This user needs to be a Linux user, too. If it doesn't exist, create it using:
```
sudo adduser microaccounts
```
* Create a database
```
createdb erapro
```
* Connect to DB:
```
psql erapro
```
```
grant all privileges on database erapro to microaccounts;
```
* To do login:
```
sabela@sabela-laptop:~$ sudo -i -u microaccounts
```
