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
Complete example:
- Original data:
```json
"xmlRootElement" : {
	"analysis" : [
		{
			"alias" : "analysis_20150219_03",
			"studyref" : {
				"accession" : "EGAS00001000618"
			}
		}
	]
}
```
- Edition query:
```json
db.analysisModel.update(
    { "_id" : ObjectId("54e5b724e4b01197ed492f0a") }, 
    {
        $set: {
            "xmlRootElement.analysis.0.studyref.accession" : "new_value"
        }
    },
    {multi: false}
)
```
In this example we only have one element in analysis array, but if there were more and we want to change all of them we can use <code>$</code> instead of <code>0</code> and set <code>multi</code> to true.
- Final result:
```json
"xmlRootElement" : {
	"analysis" : [
		{
			"alias" : "analysis_20150219_03",
			"studyref" : {
				"accession" : "new_value"
			}
		}
	]
}
```
* Order result list (1 for ascendent order, -1 for descendent):
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
