+++
title = "Stream Collectors"
categories = ["techtalk","code","java"]
tags = ["josdem","techtalks","programming","technology", "Java", "streams java", "collectors java"]
date = 2024-07-23T07:57:38-04:00
description = "This time, I will show you how to use collectors along with streams to group by, concatenate, map, and list."
+++

This time, I will show you how to use collectors along with streams to group by, concatenate, map, and list.

Example: Convert a list of elements to a string separated by ','
```java
import java.util.List;
import java.util.stream.Collectors;

public class StreamToStringConverter {

  private String parse(){
    return List.of("Java", "C++", "Lisp", "Haskell").
      stream().collect(Collectors.joining(","));
  }

  public static void main(String[] args){
    var result = new StreamToStringConverter().parse();
    assert "Java,C++,Lisp,Haskell".equals(result);
  }

}
```
From a list, generate another list selecting elements if the string length equals four.

```java
import java.util.List;
import java.util.stream.Collectors;

public class StreamToListConverter {

  private List<String> parse(){
    return List.of("Java", "C++", "Lisp", "Haskell").
      stream().filter( it -> it.length() == 4 ).toList();
  }

  public static void main(String[] args){
    var result = new StreamToListConverter().parse();
    assert 2 == result.size();
    assert result.contains("Java");
    assert result.contains("Lisp");
  }

}
```
From a list of strings get a map with string element as key and string length as value
```java
import java.util.Map;
import java.util.List;
import java.util.function.Function;
import java.util.stream.Collectors;

public class StreamToMapConverter {

  private Map<String, Integer> parse(){
    return List.of("Java", "C++", "Lisp", "Haskell").
      stream().collect(Collectors.toMap(Function.identity(), String::length));
  }

  public static void main(String[] args){
    var result = new StreamToMapConverter().parse();
    assert 4 == result.size();
    assert result.get("Java") == 4;
    assert result.get("C++") == 3;
    assert result.get("Lisp") == 4;
    assert result.get("Haskell") == 7;
  }

}
```

`Function.identity()` is a convenient way to express a function returning the same value as the input. This is useful when a function is expected, but you do not want to perform any operation to transform the input. `String::length` is a shortcut to call a class method directly; this behaves exactly as the lambda expression `it -> it.lenght()`

Example: From a list of persons, group by role.

```java
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class GroupByCollector {

  private Map<RoleType, List<Person>> parse(List<Person> persons){
    return persons.stream().collect(Collectors.groupingBy(Person::getType));
  }

  public static void main(String[] args){
    var persons = List.of(
      new Person("josdem", RoleType.DEVELOPER),
      new Person("tgtip", RoleType.DEVELOPER),
      new Person("erich", RoleType.TESTER)
    );
    var result = new GroupByCollector().parse(persons);
    assert result.get(RoleType.DEVELOPER).size() == 2;
    assert result.get(RoleType.TESTER).size() == 1;
  }

}

class Person {
  String name;
  RoleType type;

  public Person(String name, RoleType type){
    this.name = name;
    this.type = type;
  }

  RoleType getType(){
    return type;
  }

}

enum RoleType {
  DEVELOPER, TESTER
}
```

To browse the code go [here](https://github.com/josdem/java-workshop), to download the code:

```bash
git clone https://github.com/josdem/java-workshop.git
cd streams/collect
```

To run the code:

```bash
javac ${JAVA_PROGRAM}.java
java -ea ${JAVA_PROGRAM}
```

[Return to the main article](/techtalk/java)