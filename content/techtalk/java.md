+++
title = 'Java'
tags = ["josdem", "techtalks","programming","technology","generics","java","lambda"]
categories = ["techtalk", "code","java"]
description = "There are three main features in Java 8 that I love, one of them is the lambda expressions, it will have enormous implications for simplifying development. The another one is the new java.util.stream package provide a Stream API to support functional-style operations on streams of elements. The Stream API is integrated into the Collections API, which enables bulk operations on collections, such as sequential or parallel map-reduce transformations. And lastly but not least the java.time API that now are compatible with lambda, thread safe and simplified usage."
date = 2024-07-22T11:18:06-04:00
+++

There are three main features in Java 8 that I love, one of them is the lambda expressions, it will have enormous implications for simplifying development. The another one is the new `java.util.stream` package provide a Stream API to support functional-style operations on streams of elements. The Stream API is integrated into the Collections API, which enables bulk operations on collections, such as sequential or parallel map-reduce transformations. And lastly but not least the `java.time` API that now are compatible with lambda, thread safe and simplified usage.


* [Java Time API](/techtalk/java/java_time_api)
* [Streams](/techtalk/java/streams)
* [Stream Filters](/techtalk/java/stream_filters)
* [Stream Collectors](/techtalk/java/stream_collectors)
* [Executors](/techtalk/java/executors)
* [Generics](/techtalk/java/generics)
* [Functional Interfaces](/techtalk/java/functional_interfaces)
* [Lambda Expressions](/techtalk/java/lambda_expressions)
* [Junit 5](/techtalk/java/junit5)
* [From Anonymous to Lambda](/techtalk/java/from_anonymous_to_lambda)
* [JFairy Data Generator](/techtalk/java/jfairy)
* [Using Optional](/techtalk/java/optional)
* [Builder Desing Pattern](/techtalk/java/builder_design_pattern)
* [Mailosaur Getting Started](/techtalk/java/mailosaur_getting_started)
* [GitHub Repository](https://github.com/josdem/java-workshop)


**forEach() in a Map**

Iterate over a collection in Java 8 is a really nice one, which lets you pass a method reference or a lambda to receive (key, value) pairs one by one.

```java
import java.util.Map;
import java.util.HashMap;

class ForEachMap {

  void iterate(Map<String, Integer> items){
    items.forEach((k,v) -> System.out.println("Item : " + k + " Count : " + v));
  }

  public static void main(String[] args){
    Map<String, Integer> items = new HashMap<>();
    items.put("A", 10);
    items.put("B", 20);
    items.put("C", 30);

    new ForEachMap().iterate(items);
  }

}
```

**output**

```bash
Item : A Count : 10
Item : B Count : 20
Item : C Count : 30
```


**forEach() in a List**

Also, you can loop a List with forEach

```java
import java.util.List;
import java.util.ArrayList;

class ForEachList {

  void iterate(List<String> items){
    items.forEach(System.out::println);
  }

  public static void main(String[] args){
    List<String> items = new ArrayList<String>();
    items.add("A");
    items.add("B");
    items.add("C");

    new ForEachList().iterate(items);
  }

}
```

**output**

```bash
A
B
C
```

**NOTE:** You can get item from collection like this: `items.forEach(item -> System.out.println(item));`

You can iterate a Set in the same way

[Return to the main article](/)
