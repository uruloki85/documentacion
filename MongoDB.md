[MongoDB](http://docs.mongodb.org/manual/)
=======
# Connect to the mongo server with credentials
* Short form:
```bash
mongo sql12.local:27017/session -u session_user -p --authenticationMechanism=SCRAM-SHA-1
```
* Long form:
```bash
mongo --host sql12.local --port 27017 -u session_user -p --authenticationMechanism=SCRAM-SHA-1 --authenticationDatabase=session session
```
* `--authenticationDatabase=session`: not required, it defaults to dbname if provided.
# Dump
Binary dump:
```bash
@frontendtest01:~> /iso/tmp/mongodump --host devil.local --db submitter_dev --collection submissionModel \
			--query '{"submitterId":"ega-box-211"}' --out submissions_ega-box-211_dump

@app01:~> /iso/tmp/mongodump --host sql01 --db auth --collection userManagementModel \
		--out sql01_auth_user_management_model_dump
```
# Load a dump
```
mongorestore --collection userManagementModel --db auth sql01_auth_user_management_model_dump.bson
```
# Export data in CSV or JSON format
```
mongoexport --host localhost --port 27020 --db database_name --collection documentModel \
	--query '{_id:"XXXX"}' --fields "_id,alias,status" --type csv > file_output.csv
```
* Default value for `--type` is json.
  * Use `--pretty` for pretty print
* If csv output chosen, either `--fields` or `--fieldFile` is mandatory.

# Inside mongo
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
## Edit a value ($set)
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
### Complete example
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
## Delete a field ($unset)
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
## Order a list (sort())
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
## Search in an array
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
## Update an element of an array
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

## Convert a simple field into a complex structure using the original field value
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

## Look for a string contained in a field
```json
db.users.findOne({"username" : {$regex : ".*son.*"}})
```

### Count elements in an array
```json
db.document.aggregate(
	{
		$match : {_id: "the_object_id")} 
	},
	{
		$project: { count: { $size:"$name_of_the_array" } }
	}
)
```
* `$match`: write here a condition if you want to count only the elements of a specific document in a collection.
