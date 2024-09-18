+++
title = "Generics"
tags = ["josdem", "techtalks","programming","technology","generics","java"]
categories = ["techtalk", "code","java"]
description =  "Generics is a great way to prevent bugs at compilation time since provide compile-time type checking and removing risk of ClassCastException that was common while working with collection classes. Another important advantage in using generics is that you can write code that use abstractions instead using concrete classes, so you can create maintainable and extendable code."
date = 2024-07-23T09:27:34-04:00
+++
Generics are a great way to prevent bugs at runtime since they provide compile-time type checking and promote cast elimination. Another important advantage of using generics is that you can write code that uses abstractions instead of concrete classes, so you can create maintainable and extendable code.

The following example shows how we can print a collection containing different types using generics:
```java
import java.util.List;
import java.util.Arrays;
public class ListPrinter {

  private <T> void printList(List<T> collection){
    for(T item: collection){
      System.out.printf("%s ", item);
    }
  }

  public static void main(String[] args){
    var integers = List.of(1,2,3,4,5);
    var doubles = List.of(1.1,2.2,3.3,4.4,5.5);
    var chars = List.of('J','O','S','D','E','M');
    var printer = new ListPrinter();
    printer.printList(integers);
    System.out.println();
    printer.printList(doubles);
    System.out.println();
    printer.printList(chars);
  }
}
```
Output:
```bash
1 2 3 4 5
1.1 2.2 3.3 4.4 5.5
J O S D E M
```
### Generic Type Naming Convention

Java Generic Type Naming convention helps us understand code easily, and having a naming convention is one of the best practices of Java programming language. Type parameter names are uppercase letters, making them easily distinguishable from Java variables. The most commonly used type parameter names are:

* E – Element (used extensively by the Java Collections, for example List, Set etc.)
* K – Key (Used in Map)
* N – Number
* T – Type
* V – Value (Used in Map)
* S, U. – 2nd, 3rd types

### Bounded Type Parameters

What should you do when you want to restrict the types that can be used as type arguments in a parameterized type? Let's consider the following example:
```java
package com.jos.dem.generics;

public class NaturalNumber<N extends Number> {
  private final N number;

  public NaturalNumber(N number){
    this.number = number;
    if(number.intValue() < 0 ){
      throw new IllegalArgumentException();
    }
  }

  public boolean isEven() {
    return this.number.intValue() % 2 == 0;
  }
}
```
`NaturalNumber` test case:
```java
package com.jos.dem.generics;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assertions.assertThrows;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

class NaturalNumberTest {

  @Test
  @DisplayName("should throw an exception")
  void shouldNotAcceptNegativeNumbers(){
    assertThrows(IllegalArgumentException.class, () -> new NaturalNumber<Number>(-1), "should throw an exception");
  }

  @Test
  @DisplayName("should know if is even")
  void shouldKnowIfIsEven(){
    var naturalNumber = new NaturalNumber<Number>(2);
    assertTrue(naturalNumber.isEven());
  }

  @Test
  @DisplayName("should know if is odd")
  void shouldKnowIfIsOdd(){
    var naturalNumber = new NaturalNumber<>(1);
    assertFalse(naturalNumber.isEven());
  }
  
}
```

As you can see, bounded type parameters helps you to restrict the types that can be used as type arguments in a parameterized type. A method that operates on numbers might only want to accept instances of Number or its subclasses. This is what bounded type parameters are for, in that way you can use some defined methods such as `isEven()` and `intValue()`.

This is another example using Generics in a class level and in a method level:

```java
package com.jos.dem.generics;

import static org.junit.Assert.assertEquals;

import org.junit.Test;

public class BoxTest {

  @Test
  public void shouldCreateAnStringBox(){
    String nickname = "josdem";
    Box<String> box = new Box<String>();
    box.set(nickname);
    assertEquals("java.lang.String", box.getClassTypeName());
  }

  @Test
  public void shouldCreateAnIntegerInspection(){
    Integer integer = new Integer(10);
    Box<Integer> box = new Box<Integer>();
    assertEquals("java.lang.Integer", box.getClassNumberName(integer));
  }

}
```

Box implementation:

```java
package com.jos.dem.generics;

public class Box<T> {
  private T type;

  public T get(){
    return this.type;
  }

  public void set(T type){
    this.type = type;
  }

  public String getClassTypeName(){
    return type.getClass().getName();
  }

  public <U extends Number> String getClassNumberName(U unit){
    return unit.getClass().getName();
  }

}
```

**Java Generics Upper Bounded Wildcard**

Upper bounded wildcards are used to relax the restriction on the type of variable in a method. Suppose we want to write a method that will return the sum of numbers in a list.

```java
package com.jos.dem.generics;

import java.util.List;

public class CollectionAdder {

  public Double sum(List<? extends Number> numbers){
    double result = 0;
    for(Number number : numbers){
      result += number.doubleValue();
    }
    return result;
  }

 }
```

In this way, we can sum list of Integers or Doubles even though we know that `List<Integer>` and `List<Double>` are not related, this is when upper bounded wildcard is helpful.

Here is the code to test this functionality:

```java
package com.jos.dem.generics;

import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.Arrays;
import java.util.List;

import org.junit.Test;

public class CollectionAdderTest {

  @Test
  public void shouldSumIntegers(){
    CollectionAdder collectionAdder = new CollectionAdder();
    List<Integer> numbers = Arrays.asList(new Integer(10), new Integer(11), new Integer(12));
    Double result = collectionAdder.sum(numbers);
    assertEquals(new Double(33), result);
  }

  @Test
  public void shouldSumDoubles(){
    CollectionAdder collectionAdder = new CollectionAdder();
    List<Double> numbers = Arrays.asList(new Double(10), new Double(11), new Double(12));
    Double result = collectionAdder.sum(numbers);
    assertEquals(new Double(33), result);
  }

}
```
To download the code:

```bash
git clone https://github.com/josdem/java-workshop.git
cd generics
```

To run the code:

```bash
javac ${JAVA_PROGRAM}.java
java -ea ${JAVA_PROGRAM}
```

To test the code:

```bash
gradle test
```

[Return to the main article](/techtalk/java)
