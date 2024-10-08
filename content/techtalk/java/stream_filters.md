+++
title = 'Stream Filters'
ategories = ["techtalk","code","java"]
tags = ["josdem","techtalks","programming","technology", "Java", "streams java", "filters java"]
date = 2024-07-23T07:44:46-04:00
description = "A stream represents a sequence of elements and supports different operations to perform calculations in those elements."
+++

A stream represents a sequence of elements and supports different operations to perform calculations in those elements:

```java
import java.util.List;
import java.util.Arrays;
import java.util.stream.Collectors;

public class StringFilter {

  private List<String> parse(){
    return Arrays.asList("Java", "C++", "Lisp", "Haskell").
      stream().filter( p -> p.startsWith("J")).toList();
  }

  public static void main(String[] args){
    var result = new StringFilter().parse();
    assert 1 == result.size();
    assert "Java" == result.get(0);
  }

}
```

After creating a Stream of Strings, we filtered by those starting with the letter J. If we want to filter a list, array, or map based on any characteristic, we can do it easily using lambda expressions and `filter()`

Here we are filtering a list and then counting the number of elements.

```java
import java.util.List;
import java.util.Arrays;
import java.util.stream.Collectors;

public class CountFilter {

  private Long parse(){
    return Arrays.asList("Java", "C++", "Lisp", "Haskell").
      stream().filter( p -> p.length() == 4).count();
  }

  public static void main(String[] args){
    var result = new CountFilter().parse();
    assert 2L == result;
  }

}
```
Let’s review an example using numbers; we want to find the max value from a list.
```java
import java.util.List;
import java.util.OptionalInt;

public class MaxInteger {

  private OptionalInt parse(){
    return List.of(3, 13, 31, 35, 41, 50, 66, 79, 100).
      stream().mapToInt(number -> number.intValue()).max();
  }

  public static void main(String[] args){
    var result = new MaxInteger().parse();
    assert 100 == result.getAsInt();
  }

}
```
Here, we will filter our list for a range between 20 and 60 and then calculate the sum.
```java
import java.util.List;
import java.util.Arrays;

public class IntegerFilter {

  private Integer parse(){
    return List.of(3, 13, 31, 35, 41, 50, 66, 79, 100).
      stream().filter( n -> n >= 20 && n <= 60 ).mapToInt(Integer::intValue).sum();
  }

  public static void main(String[] args){
    var result = new IntegerFilter().parse();
    assert 157 == result;
  }

}
```

To browse the code go [here](https://github.com/josdem/java-workshop), to download the code:

```bash
git clone https://github.com/josdem/java-workshop.git
cd streams/filters
```

To run the code:

```bash
javac ${JAVA_PROGRAM}.java
java -ea ${JAVA_PROGRAM}
```

[Return to the main article](/techtalk/java)