+++
title = "Getting Started with Spring Webflux"
categories = ["techtalk", "code","spring webflux"]
tags = ["josdem", "techtalks","programming","technology", "Webflux", "Reactive Webflux","spring webflux"]
date = 2024-07-25T09:32:42-04:00
description = "Spring Boot now embraces reactive programming which is a non-blocking asynchronous applications and event-driven. Spring Framework uses Reactor internally for its own reactive support. Reactor is a Reactive Streams implementation that further extends the basic Publisher contract with the Flux and Mono."
+++

Spring Boot now embraces reactive programming, a non-blocking asynchronous and event-driven application. Spring Framework uses Reactor internally for its own reactive support. The reactor is a Reactive Streams implementation that further extends the primary Publisher contract with Flux and Mono. NOTE: If you need to know what tools you need to have installed in your computer in order to create a Spring Boot basic project, please refer to my previous post: [Spring Boot](/techtalk/spring/spring_boot). Let’s start creating a new Spring Boot project with Webflux, Mongo Reactive, and Lombok as dependencies:

```bash
spring init --dependencies=webflux,data-mongodb-reactive,lombok --build=gradle --type=gradle-project --language=java reactive-webflux-workshop
```

Here is the complete `build.gradle` file generated:

```groovy
plugins {
  id 'java'
  id 'org.springframework.boot' version '3.0.6'
  id 'io.spring.dependency-management' version '1.1.0'
}

group = 'com.jos.dem.webflux'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 17

configurations {
  compileOnly {
    extendsFrom annotationProcessor
  }
}

repositories {
  mavenCentral()
}

dependencies {
  implementation('org.springframework.boot:spring-boot-starter-webflux')
  compileOnly 'org.projectlombok:lombok'
  annotationProcessor 'org.projectlombok:lombok'
  implementation('org.springframework.boot:spring-boot-starter-data-mongodb-reactive')
  testImplementation 'org.springframework.boot:spring-boot-starter-test'
  testImplementation 'io.projectreactor:reactor-test'
}

tasks.named('test') {
  useJUnitPlatform()
}
```

Now let's create a simple POJO to store and retrieve information from our MongoDB.

```java
package com.jos.dem.webflux.model;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

import lombok.Data;
import lombok.AllArgsConstructor;

@Data
@Document
@AllArgsConstructor
public class Person {

  @Id
  private String nickname;
  private String email;

}
```

The `@Data` annotation generates setters and getters, `toString()`, `equals()`, `hashcode()`, and a constructor for every required field. Lombok is a great tool to avoid boilerplate code, for knowing more please go [here](https://projectlombok.org/). Spring Data now is supporting a full reactive experience with MongoDB, Couchbase, Redis, and Casandra, in this case, let’s create a `PersonRepository` using `ReactiveMongoRepository` as we going to be reactive.

```java
package com.jos.dem.webflux.repository;

import org.springframework.data.mongodb.repository.ReactiveMongoRepository;

import com.jos.dem.webflux.model.Person;

public interface PersonRepository extends ReactiveMongoRepository<Person, String> {}
```

Next, we are going to use CommandLineRunner to start our workflow. The `CommandLineRunner` is a callback interface in Spring Boot, when Spring Boot starts will call it and pass in args through a `run()` internal method.

```java
package com.jos.dem.webflux;

import org.springframework.context.annotation.Bean;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PersonApplication {

  public static void main(String[] args) {
    SpringApplication.run(PersonApplication.class, args);
  }

  @Bean
  CommandLineRunner start(){
    return args -> {
      System.out.println("Hello World!");
    };
  }

}
```

It is time to create a list of persons and store them in our MongoDB

```java
package com.jos.dem.webflux;

import org.springframework.context.annotation.Bean;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import reactor.core.publisher.Flux;

import java.util.stream.Stream;

import com.jos.dem.webflux.model.Person;
import com.jos.dem.webflux.repository.PersonRepository;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@SpringBootApplication
public class PersonApplication {

  private Logger log = LoggerFactory.getLogger(this.getClass());

  public static void main(String[] args) {
    SpringApplication.run(PersonApplication.class, args);
  }

  @Bean
  CommandLineRunner start(PersonRepository personRepository){
    return args -> {

      Flux.just(
          new Person("josdem", "joseluis.delacruz@gmail.com"),
          new Person("tgrip", "tgrip@email.com"),
          new Person("edzero", "edzero@email.com"),
          new Person("siedrix", "siedrix@email.com"),
          new Person("mkheck", "mkheck@email.com"))
        .flatMap(personRepository::save)
        .subscribe(person -> log.info("person: {}", person));

    };
  }

}
```

The logic implemented in the operators is only executed when data starts to flow, and that does not happen until you use `subscribe()` method. That’s it, now we are storing Person objects in our MongoDB. In order to run this example you need to create a database in [MongoDB with authorization](https://www.mongodb.com/features/mongodb-authentication): "enabled". Also, do not forget to add your MongoDB credentials information to your `application.properties` file:

```properties
spring.data.mongodb.database=reactive_webflux
spring.data.mongodb.host=localhost
spring.data.mongodb.username=username
spring.data.mongodb.password=password
```

To run the project:

```bash
gradle bootRun
```

**Using Maven**

You can do the same using Maven, the only difference is that you need to specify `--build=maven` parameter in the `spring init` command line:

```bash
spring init --dependencies=webflux,data-mongodb-reactive,lombok --build=maven --language=java reactive-webflux-workshop
```

And when you run your project use this command:

```bash
mvn spring-boot:run
```

This is the pom.xml file generated:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.jos.dem.webflux</groupId>
  <artifactId>reactive-webflux-workshop</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>demo</name>
  <description>Demo project for Spring Webflux</description>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.0.6</version>
    <relativePath/> <!-- lookup parent from repository -->
  </parent>

  <properties>
    <java.version>17</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-mongodb-reactive</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-webflux</artifactId>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <excludes>
            <exclude>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
            </exclude>
          </excludes>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

To browse the project go [here](https://github.com/josdem/reactive-webflux-workshop), to download the project:

```bash
git clone https://github.com/josdem/reactive-webflux-workshop.git
cd server
```

[Return to the main article](/techtalk/spring_boot)
