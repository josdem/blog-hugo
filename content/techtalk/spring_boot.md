+++
title = "Spring Boot"
categories = ["techtalk","code","spring boot"]
tags = ["josdem","techtalks","programming","technology","java validation","spring boot"]
description = "It is a Spring project aim to create easy web or stand-alone applications."
date = 2024-07-24T09:23:35-04:00
+++

## What is Spring Boot?
Open source Java framework used to create micro services but also to create easy web or stand-alone applications. It provides the next features:

* No XML configuration is required
* Netty (Starter Webflux) or Tomcat (Starter Web)
* Convention over configuration
* Easy setup
* Very lightweight
* Easy to test

## Topics

* [Spring Boot AOP](/techtalk/spring/spring_boot_aop)
* [Spring Boot RESTful](/techtalk/spring/spring_boot_restful)
* [Spring Boot Handler Exception](/techtalk/spring/spring_boot_handler_exception)
* [Spring Boot Internationalization](/techtalk/spring/spring_boot_internationalization)
* [Spring Boot JPA](/techtalk/spring/spring_boot_jpa)
* [Spring Boot Externalization](/techtalk/spring/spring_boot_externalization)
* [Spring Boot Bootstrap](/techtalk/spring/spring_boot_bootstrap)
* [Spring Boot Testing](/techtalk/spring/spring_boot_testing)
* [Spring Boot Validation](/techtalk/spring/spring_boot_validation)
* [Spring Boot Testing Validation](/techtalk/spring/spring_boot_testing_validation)
* [Spring Boot Security](/techtalk/spring/spring_boot_security)
* [Spring Boot Security using Database](/techtalk/spring/spring_boot_security_database)
* [Spring Boot Rest Client](/techtalk/spring/spring_boot_rest_client)
* [Spring Boot Actuator](/techtalk/spring/spring_boot_actuator)
* [Spring Boot JDBC Template](/techtalk/spring/spring_boot_jdbc_template)
* [Spring Boot Flyway](/techtalk/spring/spring_boot_flyway)
* [Spring Boot Liquibase](/techtalk/spring/spring_boot_liquibase)
* [Spring Boot Logback](/techtalk/spring/spring_boot_logback)
* [Spring Boot Thymeleaf Layouts](/techtalk/spring/spring_boot_thymeleaf_layouts)
* [Spring Boot Swagger](/techtalk/spring/spring_boot_swagger)
* [Spring Boot Oauth2 with Google](/techtalk/spring/spring_boot_oauth2)
* [Spring Boot Ehcache](/techtalk/spring/spring_boot_ehcache)
* [Spring Boot Appium Cucumber](/techtalk/spring/spring_boot_appium_cucumber)
* [Spring Boot Parameters](/techtalk/spring/spring_boot_parameters)
* [Spring Boot Profiles](/techtalk/spring/spring_boot_profiles)
* [Spring Boot Retrofit2 Cucumber & Junit5](/techtalk/spring/spring_boot_retrofit_cucumber_junit5)
* [Spring Boot Testing Web Layer](/techtalk/spring/spring_boot_web_testing)
* [Spring Boot Publishing an Artifactory Library](/techtalk/spring/spring_boot_artifactory_library)
* [Spring Webflux Basics](/techtalk/spring/spring_webflux_basics)
* [Spring Webflux Server](/techtalk/spring/spring_webflux_server)
* [Spring Webflux Client](/techtalk/spring/spring_webflux_client)
* [Spring Webflux Security](/techtalk/spring/spring_webflux_security)
* [Spring Webflux Security Database](/techtalk/spring/spring_webflux_security_database)
* [Spring Webflux with Thymeleaf](/techtalk/spring/spring_webflux_thymeleaf)
* [Spring Boot JMS](/techtalk/spring/spring_boot_jms)
* [Spring Webflux Cucumber](/techtalk/spring/spring_webflux_cucumber)
* [Spring Boot WebClient](/techtalk/spring/spring_boot_webclient)
* [Spring Boot WebClient Cucumber and Junit5](/techtalk/spring/webclient_cucumber_junit5)
* [Spring Webflux Multi-Module](/techtalk/spring/spring_webflux_modules)
* [Spring Weblux Internationalization](/techtalk/spring/spring_webflux_internationalization)
* [Spring Weblux Testing Web Layer](/techtalk/spring/spring_webflux_web_testing)
* [Spring Boot Hazelcast](/techtalk/spring/spring_boot_hazelcast)
* [Spring Boot JsonNode](/techtalk/spring/spring_boot_json_node)
* [Spring Boot Server-sent Event](/techtalk/spring/spring_boot_sse)
* [Spring Boot Server-sent Event Client](/techtalk/spring/spring_boot_sse_client)
* [Spring Boot H2](/techtalk/spring/spring_boot_h2)
* [Spring Webflux URI Validator](/techtalk/spring/spring_boot_uri_validator)
* [Spring Webflux Webclient Headers](/techtalk/spring/spring_webflux_webclient_headers)
* [Spring Boot XML Schema](/techtalk/spring/spring_boot_xml_schema)
* [Spring Webflux JAXB](/techtalk/spring/spring_webflux_jaxb)
* [Spring Webflux Constructor Injection](/techtalk/spring/spring_webflux_required_args_constructor)
* [Spring Webflux WebSockets](/techtalk/spring/spring_webflux_websockets)


This post shows you how to create a simple Spring Boot project with this features:

* Gradle Build
* Maven Build
* Basic configuration

You can create this project from command line like this:

**Using Gradle**

```bash
spring init --dependencies=web --build=gradle --language=java spring-boot-setup
```

That command will generate a Spring project structure under `spring-boot-setup` folder. In order to do that you need to install [SDKMAN](http://sdkman.io/) if you are using Linux or Mac, or [posh-gvm](https://github.com/flofreud/posh-gvm) if you are using Windows. After that, you can easily set up this software development kits:

* Spring Boot
* Gradle
* Maven
* Java

Here is a `build.gradle` generated example.

```groovy
plugins {
	id 'org.springframework.boot' version '2.3.3.RELEASE'
	id 'io.spring.dependency-management' version '1.0.10.RELEASE'
	id 'java'
}

group = 'com.jos.dem.springboot.setup'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '13'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}

test {
	useJUnitPlatform()
}
```

`org.springframework.boot` makes it easy to stand up our Spring application with very little configuration, `io.spring.dependency.management` plugin provides Maven dependency managent. `boot:spring-boot-starter-test` is an starter for Spring Boot applications with libraries including Junit Jupiter and Mockito. This project will generate an `DemoApplication.java` file.

```groovy
package com.jos.dem.springboot.setup;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

  public static void main(String[] args) {
    SpringApplication.run(DemoApplication.class, args);
  }

}
```
`@SpringApplication` annotation allows Spring Boot to scan recursively for beans inside this package also enables auto-configuration, attemping to guess and configure what you might need, how cool is that?. Next step, let's create a simple rest controller as follow:


```groovy
package com.jos.dem.springboot.setup;

import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;

@RestController
public class DemoController {

  @RequestMapping("/")
  public String index(){
    return "Hello World!";
  }

}
```

Now, in order to run it, use this command line.

```bash
gradle bootRun
```

Finally you can go to this address: [http://localhost:8080/](http://localhost:8080/) and you will see our hello word message.

**Using Maven**

You can do the same using Maven, the only difference is that you need to specify `--build=maven` parameter in the `spring init` command line:

```bash
spring init --dependencies=web --build=maven --language=java spring-boot-setup
```

And when you run your project use this command:

```bash
mvn spring-boot:run
```

This is the `pom.xml` file generated:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.jos.dem.springboot.setup</groupId>
	<artifactId>spring-boot-setup</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>13</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

To browse the code go [here](https://github.com/josdem/spring-boot-setup), to download the code:

```bash
git clone git@github.com:josdem/spring-boot-setup.git
```

[Return to the main article](/)
