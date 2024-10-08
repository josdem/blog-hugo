+++
title =  "Spring Boot JsonNode"
description = "Marshall and unmarshall using Jackson, JsonNode and Spring Boot"
date = 2024-07-25T11:16:37-04:00
tags = ["josdem", "techtalks","programming","technology","spring boot","json node","jackson"]
categories = ["techtalk", "code","spring boot"]
+++

In computer science, marshalling is the process of transforming the representation of an object to a data format suitable for storage or transmission. In this technical post we will describe how we can marshall and unmarshall using [Jackson](https://en.wikipedia.org/wiki/Jackson_(API)), JsonNode and Spring Boot. If you want to know more about how to create Spring Webflux please go to my previous post getting started with Spring Webflux [here](/techtalk/spring/spring_webflux_basics). First let's create a new Spring Boot Webflux project:

```bash
spring init --dependencies=webflux,lombok --build=gradle --language=java spring-boot-json-node
```

This is the `build.gradle` file generated:

```groovy
plugins {
  id 'org.springframework.boot' version '2.1.3.RELEASE'
  id 'java'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.jos.dem.springboot.json.node'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
  compileOnly {
    extendsFrom annotationProcessor
  }
}

repositories {
  mavenCentral()
}

dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-webflux'
  compileOnly 'org.projectlombok:lombok'
  annotationProcessor 'org.projectlombok:lombok'
  testImplementation 'org.springframework.boot:spring-boot-starter-test'
  testImplementation 'io.projectreactor:reactor-test'
}
```

Now let's add Junit5 dependencies and support:

```groovy
plugins {
  id 'org.springframework.boot' version '2.1.3.RELEASE'
  id 'java'
}

apply plugin: 'io.spring.dependency-management'

def junitJupiterVersion = '5.4.0'

group = 'com.jos.dem.springboot.json.node'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
  compileOnly {
    extendsFrom annotationProcessor
  }
}

repositories {
  mavenCentral()
}

dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-webflux'
  compileOnly('org.projectlombok:lombok')
  annotationProcessor('org.projectlombok:lombok')
  testImplementation('org.springframework.boot:spring-boot-starter-test'){
    exclude group: 'junit', module: 'junit'
  }
  testImplementation "org.junit.jupiter:junit-jupiter-api:$junitJupiterVersion"
  testRuntime "org.junit.jupiter:junit-jupiter-engine:$junitJupiterVersion"
  testImplementation 'io.projectreactor:reactor-test'
  testCompile 'org.junit.platform:junit-platform-commons:1.4.0'
  testCompile 'org.junit.platform:junit-platform-launcher:1.4.0'
}

test {
  useJUnitPlatform()
}
```

**From Json to JsonNode**

Let's validate Json to JsonNode transformation, in order to parse a Json string we only need an `ObjectMapper`

```java
ObjectMapper mapper = new ObjectMapper();
String json = "{\"id\":1196,\"nickname\":\"josdem\",\"email\":\"joseluis.delacruz@gmail.com\"}";
JsonNode node = mapper.readTree(json);
```

Please, consider this test case to validate our transformation to JsonNode.

```java
@Test
@DisplayName("Validate Json to JsonNode transformation")
void shouldGetJsonNodeFromJson() throws Exception {
  log.info("Running: Validate json to json node transformation at {}", new Date());

  assertAll("node",
    () -> assertEquals(1196, node.get("id").intValue(), "Should get id"),
    () -> assertEquals("josdem", node.get("nickname").textValue(), "Should get nickname"),
    () -> assertEquals("joseluis.delacruz@gmail.com", node.get("email").textValue(), "should get email")
  );

}
```

**JsonNode to Bean**

Next, let's validate a JsonNode to [POJO](https://en.wikipedia.org/wiki/Plain_old_Java_object) transformation

```java
package com.jos.dem.springboot.json.node.model;

import lombok.Setter;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class Person {
  private Integer id;
  private String nickname;
  private String email;
}
```

Here is our test case

```java
@Test
@DisplayName("Validate JsonNode to Person transformation")
void shouldGetPersonFromJsonNode() throws Exception {
  log.info("Running: Validate json to json node transformation at {}", new Date());

  Person person = mapper.treeToValue(node, Person.class);

  assertAll("person",
    () -> assertEquals(1196, person.getId(), "Should get id"),
    () -> assertEquals("josdem", person.getNickname(), "Should get nickname"),
    () -> assertEquals("joseluis.delacruz@gmail.com", person.getEmail(), "should get email")
  );

}
```

**Bean to JsonNode**


Now, let's validate a [POJO](https://en.wikipedia.org/wiki/Plain_old_Java_object) to JsonNode transformation

```java
@Test
@DisplayName("Validate Person to JsonNode transformation")
void shouldGetJsonNodeFromPerson() throws Exception {
  log.info("Running: Validate person to json node transformation at {}", new Date());
  Person person = new Person(1196, "josdem","joseluis.delacruz@gmail.com");
  JsonNode node = mapper.valueToTree(person);

  assertAll("person",
    () -> assertEquals(1196, node.get("id").intValue(), "Should get id"),
    () -> assertEquals("josdem", node.get("nickname").textValue(), "Should get nickname"),
    () -> assertEquals("joseluis.delacruz@gmail.com", node.get("email").textValue(), "should get email")
  );

}
```

**Parameters to JsonNode**

Finally let's create a JsonNode with just arguments

```java
@Test
@DisplayName("Validate Arguments to Json Node transformation")
void shouldGetJsonNodeFromArguments() throws Exception {
  log.info("Running: Validate arguments to json node transformation at {}", new Date());
  Integer id = 1196;
  String nickname = "josdem";
  String email = "joseluis.delacruz@gmail.com";

  JsonNode node = mapper.createObjectNode();
  ((ObjectNode) node).put("id", 1196);
  ((ObjectNode) node).put("nickname", "josdem");
  ((ObjectNode) node).put("email", "joseluis.delacruz@gmail.com");

  assertAll("person",
    () -> assertEquals(1196, node.get("id").intValue(), "Should get id"),
    () -> assertEquals("josdem", node.get("nickname").textValue(), "Should get nickname"),
    () -> assertEquals("joseluis.delacruz@gmail.com", node.get("email").textValue(), "should get email")
  );

}
```

Here is our complete test case

```java
package com.jos.dem.springboot.json.node;

import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.Date;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ObjectNode;

import com.jos.dem.springboot.json.node.model.Person;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class JsonNodeTest {

  private JsonNode node;
  private ObjectMapper mapper = new ObjectMapper();
  private Logger log = LoggerFactory.getLogger(this.getClass());

  @BeforeEach
  void init() throws Exception {
    log.info("Getting Json Node from Json");
    String json = "{\"id\":1196,\"nickname\":\"josdem\",\"email\":\"joseluis.delacruz@gmail.com\"}";
    node = mapper.readTree(json);
  }


  @Test
  @DisplayName("Validate Json to JsonNode transformation")
  void shouldGetJsonNodeFromJson() throws Exception {
    log.info("Running: Validate json to json node transformation at {}", new Date());

    assertAll("node",
      () -> assertEquals(1196, node.get("id").intValue(), "Should get id"),
      () -> assertEquals("josdem", node.get("nickname").textValue(), "Should get nickname"),
      () -> assertEquals("joseluis.delacruz@gmail.com", node.get("email").textValue(), "should get email")
    );

  }

  @Test
  @DisplayName("Validate JsonNode to Person transformation")
  void shouldGetPersonFromJsonNode() throws Exception {
    log.info("Running: Validate json to json node transformation at {}", new Date());

    Person person = mapper.treeToValue(node, Person.class);

    assertAll("person",
      () -> assertEquals(1196, person.getId(), "Should get id"),
      () -> assertEquals("josdem", person.getNickname(), "Should get nickname"),
      () -> assertEquals("joseluis.delacruz@gmail.com", person.getEmail(), "should get email")
    );

  }

  @Test
  @DisplayName("Validate Person to JsonNode transformation")
  void shouldGetJsonNodeFromPerson() throws Exception {
    log.info("Running: Validate person to json node transformation at {}", new Date());
    Person person = new Person(1196, "josdem","joseluis.delacruz@gmail.com");
    JsonNode node = mapper.valueToTree(person);

    assertAll("person",
      () -> assertEquals(1196, node.get("id").intValue(), "Should get id"),
      () -> assertEquals("josdem", node.get("nickname").textValue(), "Should get nickname"),
      () -> assertEquals("joseluis.delacruz@gmail.com", node.get("email").textValue(), "should get email")
    );

  }

  @Test
  @DisplayName("Validate Arguments to Json Node transformation")
  void shouldGetJsonNodeFromArguments() throws Exception {
    log.info("Running: Validate arguments to json node transformation at {}", new Date());
    Integer id = 1196;
    String nickname = "josdem";
    String email = "joseluis.delacruz@gmail.com";

    JsonNode node = mapper.createObjectNode();
    ((ObjectNode) node).put("id", 1196);
    ((ObjectNode) node).put("nickname", "josdem");
    ((ObjectNode) node).put("email", "joseluis.delacruz@gmail.com");

    assertAll("person",
      () -> assertEquals(1196, node.get("id").intValue(), "Should get id"),
      () -> assertEquals("josdem", node.get("nickname").textValue(), "Should get nickname"),
      () -> assertEquals("joseluis.delacruz@gmail.com", node.get("email").textValue(), "should get email")
    );

  }

  @AfterEach
  void finish() throws Exception {
    log.info("Test execution finished");
  }

}
```

**Dealing with complex Json structures**

Let's consider this Json structure

```json
{
  "batchId": "fbe07c89-ffa7-4c86-9832-5f75cf765737",
  "eventType": "EmployeeClockIn",
  "publishedAt": "2019-03-09T07:36:43-05:00",
  "messages" : [{
  	"messageId" : "4aeaa175-e46d-42eb-83d3-cd02865d4863",
  	"eventAt": "2019-03-09T07:36:43-05:00",
  	"data": {
  		"firstname": "Jose",
  		"lastname": "Morales",
  		"nickname": "josdem",
  		"employeeNumber": 1196,
  		"email": "joseluis.delacruz@gmail.com",
  		"clockInDateTime": "2019-03-09T07:36:43-05:00"
  	}
  }]
}
```

This could be our bean representation: `Event`

```java
package com.jos.dem.springboot.json.node.model;

import java.time.OffsetDateTime;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Event {
  private String batchId;
  private String eventType;
  private OffsetDateTime publishedAt;
  private Message[] messages;
}
```

`Message`

```java
package com.jos.dem.springboot.json.node.model;

import java.time.OffsetDateTime;

import lombok.Getter;
import lombok.Setter;

import com.fasterxml.jackson.databind.JsonNode;

@Getter
@Setter
public class Message {
  private String messageId;
  private OffsetDateTime eventAt;
  private JsonNode data;
}
```

So to unmarshall this complex Json structure the best strategy is to create a service to delegate that responsibility

```java
package com.jos.dem.springboot.json.node.service;

import java.io.File;
import java.io.IOException;

import com.jos.dem.springboot.json.node.model.Event;

public interface UnmarshallerService {

  Event read(File jsonFile) throws IOException;

}
```

Here is our service implementation

```java
package com.jos.dem.springboot.json.node.service.impl;

import java.io.File;
import java.io.InputStream;
import java.io.FileInputStream;
import java.io.IOException;

import javax.annotation.PostConstruct;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;

import org.springframework.stereotype.Service;

import com.jos.dem.springboot.json.node.model.Event;
import com.jos.dem.springboot.json.node.service.UnmarshallerService;

@Service
public class UnmarshallerServiceImpl implements UnmarshallerService {

  private ObjectMapper mapper = new ObjectMapper();

  @PostConstruct
  public void setup() {
    mapper.registerModule(new JavaTimeModule());
  }

  public Event read(File jsonFile) throws IOException {
    InputStream inputStream = new FileInputStream(jsonFile);
    JsonNode jsonNode = mapper.readTree(inputStream);
    return mapper.treeToValue(jsonNode, Event.class);
  }

}
```

And here is our complete test case to unmarshall it

```java
package com.jos.dem.springboot.json.node;

import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.Date;
import java.time.OffsetDateTime;

import com.fasterxml.jackson.databind.JsonNode;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.extension.ExtendWith;

import java.io.File;
import java.io.InputStream;
import java.io.FileInputStream;

import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.beans.factory.annotation.Autowired;

import com.jos.dem.springboot.json.node.model.Event;
import com.jos.dem.springboot.json.node.model.Message;
import com.jos.dem.springboot.json.node.service.UnmarshallerService;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@ExtendWith(SpringExtension.class)
@SpringBootTest
public class UnmarshallerServiceTest {

  private Logger log = LoggerFactory.getLogger(this.getClass());

  @Autowired
  private UnmarshallerService service;

  private Event event;
  private Message message;
  private Message[] messages;

  @BeforeEach
  void init() throws Exception {
    log.info("Getting Event from ClockIn json file");
    File jsonFile = new File("src/main/resources/ClockIn.json");
    event = service.read(jsonFile);
    Message[] messages = event.getMessages();
    message = messages[0];
  }

  @Test
  @DisplayName("Validate Event values from ClockIn Json file")
  void shouldGetEventFromClockInFile() throws Exception {
    log.info("Running: Validate Event values from ClockIn Json file at {}", new Date());

    assertAll("event",
      () -> assertEquals("fbe07c89-ffa7-4c86-9832-5f75cf765737", event.getBatchId(), "Should get batch id"),
      () -> assertEquals("EmployeeClockIn", event.getEventType(), "Should get event type"),
      () -> assertEquals(OffsetDateTime.parse("2019-03-09T12:36:43Z"), event.getPublishedAt(), "Should get published at time")
    );

  }

  @Test
  @DisplayName("Validate Message values from Event")
  void shouldGetMessageFromEvent() throws Exception {
    log.info("Running: Validate Message values from event at {}", new Date());

    assertAll("message",
      () -> assertEquals("4aeaa175-e46d-42eb-83d3-cd02865d4863", message.getMessageId(), "Should get message id"),
      () -> assertEquals(OffsetDateTime.parse("2019-03-09T12:36:43Z"), event.getPublishedAt(), "Should get published at time")
    );

  }

  @Test
  @DisplayName("Validate Data values from message")
  void shouldGetDataFromMessage() throws Exception {
    log.info("Running: Validate Data values from message at {}", new Date());

    JsonNode data = message.getData();
    assertAll("data",
      () -> assertEquals("Jose", data.get("firstname").textValue(), "Should get Firstname"),
      () -> assertEquals("Morales", data.get("lastname").textValue(), "Should get Lastname"),
      () -> assertEquals("josdem", data.get("nickname").textValue(), "Should get Nickname"),
      () -> assertEquals(1196, data.get("employeeNumber").intValue(), "Should get Employee Number"),
      () -> assertEquals("joseluis.delacruz@gmail.com", data.get("email").textValue(), "Should get Email"),
      () -> assertEquals("2019-03-09T07:36:43-05:00", data.get("clockInDateTime").textValue(), "Should get clockIn time")
    );

  }

  @AfterEach
  void finish() throws Exception {
    log.info("Test execution finished");
  }

}
```

**Using Maven**

You can do the same using Maven, the only difference is that you need to specify `--build=maven` parameter in the spring init command line:

```bash
spring init --dependencies=webflux,lombok --build=maven --language=java spring-boot-json-node
```

This is the `pom.xml` file generated along with Junit5 as dependency on it added manualy:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.jos.dem.springboot</groupId>
  <artifactId>jsonNode</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>spring-boot-json-node</name>
  <description>This project shows how to work with Jackson JsonNode</description>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.7.RELEASE</version>
    <relativePath/>
  </parent>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <maven.surefire.version>2.22.0</maven.surefire.version>
    <maven-failsafe-plugin.version>2.22.0</maven-failsafe-plugin.version>
    <java.version>11</java.version>
    <junit.jupiter.version>5.4.1</junit.jupiter.version>
  </properties>

  <dependencies>
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
      <exclusions>
        <exclusion>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <dependency>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-test</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-engine</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-commons</artifactId>
      <version>1.4.0</version>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-launcher</artifactId>
      <version>1.4.0</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-failsafe-plugin</artifactId>
      </plugin>
    </plugins>
  </build>

</project>
```

To run the project with Gradle:

```bash
gradle test
```

To run the project with Maven:

```bash
mvn test
```

To browse the project go [here](https://github.com/josdem/spring-boot-json-node), to download the project:

```bash
git clone git@github.com:josdem/spring-boot-json-node.git
```


[Return to the main article](/techtalk/spring_boot)
