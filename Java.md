# Java
## Deserialize response from spring-data-rest
Response format is:
```json
{
  "_embedded" : {
    "dac" : [ {
      "alias" : null,
      "title" : "This is the title",
      "url" : "http://invented.org",
      "_links" : {
        "self" : {
          "href" : "http://localhost:9450/dbmngservice/v1/dac/635"
        },
        "dacView" : {
          "href" : "http://localhost:9450/dbmngservice/v1/dac/635"
        },
        "dacContact" : {
          "href" : "http://localhost:9450/dbmngservice/v1/dac/635/dacContact"
        }
      }
    } ]
  },
  "_links" : {
    "first" : {
      "href" : "http://localhost:9450/dbmngservice/v1/dac?page=0&size=1"
    },
    "self" : {
      "href" : "http://localhost:9450/dbmngservice/v1/dac{&sort}",
      "templated" : true
    },
    "next" : {
      "href" : "http://localhost:9450/dbmngservice/v1/dac?page=1&size=1"
    },
    "last" : {
      "href" : "http://localhost:9450/dbmngservice/v1/dac?page=811&size=1"
    },
    "profile" : {
      "href" : "http://localhost:9450/dbmngservice/v1/profile/dac"
    },
    "search" : {
      "href" : "http://localhost:9450/dbmngservice/v1/dac/search"
    }
  },
  "page" : {
    "size" : 1,
    "totalElements" : 812,
    "totalPages" : 812,
    "number" : 0
  }
}
```
Java code for reading this:
```java
// Log json message in pretty print format
Gson gson = new GsonBuilder().setPrettyPrinting().create();
String json = gson.toJson(result);
log.debug(json);

// Parse the message
JSONObject jsonObj;
try {
  jsonObj = new JSONObject(result).getJSONObject("_embedded");
  JSONArray jsonArray = jsonObj.getJSONArray("dac");
  for (int i = 0; i < jsonArray.length(); i++) {
    JSONObject obj = jsonArray.getJSONObject(i);
    DacView dac = new DacView(obj);
    list.add(dac);
//          log.debug("DAC title: {}", obj.getString("title"));
  }
} catch (JSONException e) {
  log.error("Error while parsing response to JSON", e);
}
```
### Lambda expressions (Java 8)
* Extract one field from a list without looping over it:
```java
List<String> filenames = fileDataList.stream()
                                      .map(file -> file.getFilename())
                                      .collect(Collectors.toList());
```
* Convert a List into a comma separated String:
```
String statuses = statusList.stream()
                            .map(status -> status.getStatus())
                            .collect(Collectors.joining(","));
```

### Guava library
* Extract one field from a list without looping over it:
```java
// TODO replace with lambda expression after upgrading to java 8
List<String> filenames = Lists.transform(fileDataList, new Function<FileData, String>() {
  @Override
  public String apply(FileData input) {
    return input.getFilename();
  }
});
```
* Extract one field from a List and convert to a Set
```java
  Set<String> experimentModelList =
      FluentIterable.from(runs).transform(new Function<RunData, String>() {
        @Override
        public String apply(RunData input) {
          return input.getExperimentId();
        }
    }).toSet();
```
* Convert a List to a Set
```java
FluentIterable.from(runIds).toSet()
```
