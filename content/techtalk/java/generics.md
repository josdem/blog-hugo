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
As you can see, bounded type parameters help you to restrict the types that can be used as type arguments in a parameterized type. A method that operates on numbers only wants to accept instances of Number or their subclasses. This is what bounded type parameters are for; in that way, you can use some defined methods such as `isEven()` and `intValue()`.

This is another example of using Generics at a class level and at a method level:
```java
package com.jos.dem.generics;

import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

class BoxTest {

  @Test
  @DisplayName("should contains a string in the box")
  void shouldContainsAStringInTheBox(){
    String nickname = "josdem";
    var box = new Box<String>();
    box.set(nickname);
    assertEquals(nickname, box.get());
  }

  @Test
  @DisplayName("should contains an integer in the box")
  void shouldContainsAnIntegerInTheBox(){
    var box = new Box<Integer>();
    box.set(10);
    assertEquals(10, box.get());
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

}
```
**Java Generics Upper Bounded Wildcard**

Upper-bounded wildcards are used to relax the restriction on the type of variable in a method. Suppose we want to write a method that will return the sum of numbers in a list.
```java
package com.jos.dem.generics;

import java.util.List;

public class CollectionAdder {

  public Double sum(List<? extends Number> numbers){
    return numbers.stream()
      .mapToDouble(Number::doubleValue)
      .sum();
  }
    
 }
```
In this way, we can sum a list of Integers or Doubles even though we know that `List<Integer>` and `List<Double>` are different types; this is when the upper bounded wildcard is helpful.

Here is the code to test this functionality:
```java
package com.jos.dem.generics;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CollectionAdderTest {

    private final CollectionAdder collectionAdder = new CollectionAdder();

    @Test
    @DisplayName("should sum integers")
    void shouldSumIntegers() {
        var numbers = List.of(10, 11, 12);
        assertEquals(Double.valueOf(33), collectionAdder.sum(numbers));
    }

    @Test
    @DisplayName("should sum doubles")
    void shouldSumDoubles() {
        List<Double> numbers = List.of(10.5, 11.2, 12.8);
        assertEquals(Double.valueOf(34.5), collectionAdder.sum(numbers));
    }

}
```
To browse the project [here](https://github.com/josdem/java-workshop/tree/main/generics), to download the project:
```bash
git clone https://github.com/josdem/java-workshop.git
cd generics
```

To test the code:
```bash
gradle test
```
[Return to the main article](/techtalk/java)
