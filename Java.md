#Java

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
