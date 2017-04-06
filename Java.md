# Java

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
