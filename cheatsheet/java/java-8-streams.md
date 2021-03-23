---
layout: topic
title: streams
parent: java
---

### List of Obj where field is true

```java
List<SomeObj> validObjs = someListOfObj
  .stream()
  .collect(Collectors.groupingBy(SomeObj::isValid, Collectors.toList()));
  .get(true);
```
