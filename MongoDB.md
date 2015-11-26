[MongoDB] (http://docs.mongodb.org/manual/)
=======

###Most used commands:
* List all DBs available
```json
show dbs
```
* Show DB in use
```json
db
```
* List all collections in DB
```json
show collections
```
* Delete collection content
```json
db.collection_name.drop()
```
###Dump
Binary dump:
```bash
sdelatorre@frontendtest01:~> /iso/tmp/mongodump --host devil.local --db submitter_dev --collection submissionModel --query '{"submitterId":"ega-box-211"}' --out submissions_ega-box-211_dump

sdelatorre@app01:~> /iso/tmp/mongodump --host sql01 --db auth --collection userManagementModel --query '{},{_class:0}' --out sql01_auth_user_management_model_dump
```
###Load a dump
```
mongorestore --collection userManagementModel --db auth sql01_auth_user_management_model_dump.bson
```
###Edit a value ($set)
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
#####Complete example
* Original data
```json
"_id" : ObjectId("54e5b724e4b01197ed492f0a"),
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
* Edition query
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
Use <code>{multi: true}</code> if there are several documents that matches the query (otherwise it will be updated only the first one found).
Use <code>$</code> if you don't know the position in the array of the element you want to change (the position depends on the query).
* Final result
```json
"_id" : ObjectId("54e5b724e4b01197ed492f0a"),
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
###Delete a field ($unset)
* We want to remove the field "0" that apperars inside "studyref"
```json
"_id" : ObjectId("54e5b724e4b01197ed492f0a"),
"xmlRootElement" : {
	"studyref" : {
		"0" : {
			"accession" : "EGAS00001000618"
		},
		"accession" : "EGAS00001000618"
	}
}
```
* Delete query
```json
db.analysisModel.update(
   { "_id" : ObjectId("54e5b724e4b01197ed492f0a") },
   { $unset: { "xmlRootElement.analysis.0.studyref.0": ""} }
)
```
###Order a list (sort())
1 for ascendent order, -1 for descendent:
```json
db.notificationModel.find().sort({ "serviceMessage.header.timestamp": -1 }).pretty()
```
Where documents follow this structure
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
###Search in an array
If the array contains Strings:
```json
db.userModel.find( {	
	authorities : { "ROLE_ADMIN" } 
} ).pretty()
```
If the array contains an object:
```json
db.userModel.find( {	
	authorities : { 
		$elemMatch: { 
			role: "ROLE_ADMIN"
		} 
	}
} ).pretty()
```
###Update an element of an array
Original document:
```json
{
	"_id" : "4",
	"csvColumnNames" : [
		"Sample",
		"Fastq File"
	]
}

```
To replace "Sample" with "Sample alias":
```json
db.runFileTypeModel.update(
   { csvColumnNames: "Sample" },
   { $set: { "csvColumnNames.$" : "Sample alias" } },
   { multi : true}
)
```
Use $ when you don't know the position of the element in the array.

###Convert a simple field into a complex structure using the original field value
Original data:
```json
{
	"_id" : ObjectId("555323f70cf2f123d8966778"),
	"files" : [ "UK10K_SCOOP5013854.vcf.gz" ]
}

```
Function to execute:
```json
db.runModel.find({}).snapshot().forEach( function (x) { 
	x.files = [ { "filename": x.files[0] } ]; 
	db.runModel.save(x); 
});
```
Resulting data:
```json
{
	"_id" : ObjectId("555323f70cf2f123d8966778"),
	"files" : [
		{
			"filename" : "UK10K_SCOOP5013854.vcf.gz"
		}
	]
}

```
