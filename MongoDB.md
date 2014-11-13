[MongoDB] (http://docs.mongodb.org/manual/)
=======

* List all DBs available:
```json
show dbs
```
* Show DB in use:
```json
db
```
* List all collections in DB:
```json
show collections
```
* Delete collection content:
```json
db.collection_name.drop()
```
* Edit a value in a collection:
```json
db.collection_name.update(
	{ "field_name" : "condition_value" }, // Query
	{
		$set: {
			"field_name" : "new_value"
		}
	},
	{multi: true}
)
```
