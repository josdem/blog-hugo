+++
title =  "Spring Boot Hazelcast"
description = "Hazelcast is in-memory distributed computing platform for managing data and performing parallel execution for application speed and scale"
date = 2024-07-25T11:15:21-04:00
tags = ["josdem", "techtalks","programming","technology"]
categories = ["techtalk", "code"]
+++

`java.util.Map` is not thread safe, if you want to use thread safe Map you can use `ConcurrentHashMap` what if you want to use a map through multiple JVM? then your best option is [Hazelcast](https://hazelcast.org/), besides Hazelcast is Open Source written in Java and Maven friendly. In this technical post we are going to view how we can use Hazelcast among with Spring Boot. If you want to know more about how to create Spring Webflux please go to my previous post getting started with Spring Webflux [here](/techtalk/spring/spring_boot). First let's create a new Spring Boot project:

```bash
spring init --dependencies=web,lombok --type=gradle-project-kotlin --language=java --javaVersion=21 spring-boot-hazelcast
```

This is the `build.gradle.kts` file generated:

```kotlin
plugins {
    java
    id("org.springframework.boot") version "3.5.5"
    id("io.spring.dependency-management") version "1.1.7"
}

group = "com.jos.dem.springboot.hazelcast"
version = "0.0.1-SNAPSHOT"
description = "Demo project for Spring Boot"

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21)
    }
}

configurations {
    compileOnly {
        extendsFrom(configurations.annotationProcessor.get())
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    compileOnly("org.projectlombok:lombok")
    annotationProcessor("org.projectlombok:lombok")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

tasks.withType<Test> {
    useJUnitPlatform()
}
```

Next step is to add Hazelcast dependency:

```kotlin
implementation("com.hazelcast:hazelcast-spring")
```

Hazelcast can be configured through XML or using Java configuration or even mix of both. Please consider this configuration using Java config.

```java
package com.jos.dem.springboot.hazelcast.conf;

import com.hazelcast.config.Config;
import com.hazelcast.config.EvictionConfig;
import com.hazelcast.config.EvictionPolicy;
import com.hazelcast.config.MapConfig;
import com.hazelcast.config.MaxSizePolicy;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@Component
public class HazelcastConfiguration {

    private final static int MAX_SIZE = 200;

    @Bean
    public Config hazelCastConfig() {
        var evictionConfig = new EvictionConfig()
                .setEvictionPolicy(EvictionPolicy.LFU)
                .setMaxSizePolicy(MaxSizePolicy.USED_HEAP_PERCENTAGE)
                .setSize(MAX_SIZE);

        return new Config().setInstanceName("hazelcast-instance")
                .addMapConfig(
                        new MapConfig()
                                .setName("configuration")
                                .setEvictionConfig(evictionConfig)
                                .setTimeToLiveSeconds(-1));
    }

}
```

To create a `HazelcastInstance` you should set an instance name in our `Config` object, besides to the instance name, you can specify the eviction configuration.

**Eviction**

Unless you delete the map entries manually or use an eviction policy, they will remain in the map. Hazelcast supports policy based eviction for distributed maps. Currently supported policies are LRU (Least Recently Used) and LFU (Least Frequently Used). Now, let's create a controller to store values in hazelcast and get them back.

```java
package com.jos.dem.springboot.hazelcast.controller;

import com.hazelcast.core.HazelcastInstance;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@Slf4j
@RestController
@RequestMapping("/hazelcast")
@RequiredArgsConstructor
public class HazelcastController {

  private final HazelcastInstance hazelcastInstance;

  @PostMapping("/write/{key}/{value}")
  public String write(@PathVariable("key") String key, @PathVariable("value") String value) {
    log.info("Storing key: {} with value: {}", key, value);
    Map<String, String> map = hazelcastInstance.getMap("memory");
    map.putIfAbsent(key, value);
    return "Key and value stored";
  }

  @GetMapping("/read/{key}")
  public String read(@PathVariable("key") String key) {
    log.info("Reading stored value with key: {}", key);
    Map<String, String> map = hazelcastInstance.getMap("memory");
    return map.get(key);
  }
}
```

That's it, if you start our Spring Boot application with this command:

```bash
./gradlew bootRun
```

You will be able to store a new value:

```bash
curl -X POST http://localhost:8080/hazelcast/write/key/value
```

And retrieve it

```bash
curl http://localhost:8080/hazelcast/read/key
```

Result:

```bash
2025-08-25T13:50:36.719-06:00  INFO 14992 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port 8080 (http) with context path '/'
2025-08-25T13:50:36.724-06:00  INFO 14992 --- [           main] c.j.d.s.hazelcast.HazelcastApplication   : Started HazelcastApplication in 4.717 seconds (process running for 4.932)
2025-08-25T13:51:11.964-06:00  INFO 14992 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2025-08-25T13:51:11.964-06:00  INFO 14992 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2025-08-25T13:51:11.965-06:00  INFO 14992 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 1 ms
2025-08-25T13:51:11.989-06:00  INFO 14992 --- [nio-8080-exec-1] c.j.d.s.h.c.HazelcastController          : Storing key: key with value: value
2025-08-25T13:51:12.000-06:00  INFO 14992 --- [nio-8080-exec-1] c.h.i.p.impl.PartitionStateManagerImpl   : [192.168.100.7]:5701 Initializing cluster partition table arrangement...
2025-08-25T13:51:21.396-06:00  INFO 14992 --- [nio-8080-exec-2] c.j.d.s.h.c.HazelcastController          : Reading stored value with key: key
```

These are the tests cases:

```kotlin
package com.josdem.springboot.hazelcast.controller

import org.junit.jupiter.api.MethodOrderer
import org.junit.jupiter.api.Order
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.TestInfo
import org.junit.jupiter.api.TestMethodOrder
import org.slf4j.LoggerFactory
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post
import org.springframework.test.web.servlet.result.MockMvcResultMatchers.status

@SpringBootTest
@AutoConfigureMockMvc
@TestMethodOrder(MethodOrderer.OrderAnnotation::class)
internal class HazelcastControllerTest {

    @Autowired
    private lateinit var mockMvc: MockMvc

    private val log = LoggerFactory.getLogger(this::class.java)

    @Test
    @Order(1)
    fun `should store a value in the map`(testInfo: TestInfo) {
        log.info(testInfo.displayName)
        val request =
            post("/hazelcast/write/1/value1")
        mockMvc
            .perform(request)
            .andExpect(status().isOk())
    }

    @Test
    @Order(2)
    fun `should get a value from the map`(testInfo: TestInfo) {
        log.info(testInfo.displayName)
        val request =
            get("/hazelcast/read/1")
        mockMvc
            .perform(request)
            .andExpect(status().isOk())
    }

}
```

To run the test cases:

```bash
./gradlew test
```

To browse the project go [here](https://github.com/josdem/spring-boot-hazelcast), to download the project:

```bash
git clone git@github.com:josdem/spring-boot-hazelcast.git
```


[Return to the main article](/techtalk/spring_boot)
