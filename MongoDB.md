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
	{multi: true} // All documents that matches the condition
)
```
* Order result list:
```json
db.notificationModel.find().sort({ "serviceMessage.header.timestamp": -1 }).pretty()
```
Where documents follow this structure:
```json
{
	"_id" : ObjectId("5464bae5e4b014f477fe07b9"),
	"serviceMessage" : {
		...
		"header" : {
			"timestamp" : ISODate("2014-11-13T14:06:28Z")
		}
	}
}
```
