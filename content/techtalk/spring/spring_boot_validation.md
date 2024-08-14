+++
title = "Spring Boot Validation"
categories = ["techtalk","code","spring boot"]
tags = ["josdem","techtalks","programming","technology","java validation","spring boot"]
description = "Spring has a Validation interface that we can use to create custom object validation; if some errors occur in the validation process, it stores them in a BindingResult object so you can test for and retrieve validation errors."
date = 2024-07-24T12:51:34-04:00
+++

Spring has a `Validation` interface that we can use to create custom object validation; if some errors occur in the validation process, it stores them in a `BindingResult` object so you can test for and retrieve validation errors. Let's consider the following validator implementation:

```java
package com.josdem.springboot.validation.validator;

import com.josdem.springboot.validation.command.PersonCommand;
import org.springframework.validation.Validator;
import org.springframework.validation.Errors;
import org.springframework.stereotype.Component;

@Component
public class PersonValidator implements Validator {

  private final String REGEX = "[0-9]+";

  @Override
  public boolean supports(Class<?> clazz) {
    return PersonCommand.class.equals(clazz);
  }

  @Override
  public void validate(Object target, Errors errors) {
    PersonCommand personCommand = (PersonCommand) target;
    validateEin(errors, personCommand);
  }

  private void validateEin(Errors errors, PersonCommand command) {
    if(!command.getEin().matches(REGEX)){
      errors.rejectValue("ein", "ein.error.format");
    }
  }

}
```

This class provides validation behavior implementing `org.springframework.validation.Validator`

* `supports(Class)` Define which class can be validated
* `validate(Object, org.springframework.validation.Errors)` Object validation, if some errors occurs store them in `Errors`
* `validateEin(Errors errors, PersonCommand command)` validates EIN(Employer Identification Number) which should be a numeric expression.

```java
package com.josdem.springboot.validation.controller;

import com.josdem.springboot.validation.command.PersonCommand;
import com.josdem.springboot.validation.model.Person;
import com.josdem.springboot.validation.repository.PersonRepository;
import com.josdem.springboot.validation.validator.PersonValidator;
import jakarta.validation.Valid;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import java.util.List;

@Slf4j
@Controller
@RequestMapping("/persons")
public record PersonController(PersonRepository personRepository,
                               PersonValidator personValidator) {

    @InitBinder
    private void initBinder(WebDataBinder binder) {
        binder.addValidators(personValidator);
    }

    @GetMapping
    ModelAndView getAll() {
        log.info("Listing all persons");
        ModelAndView modelAndView = new ModelAndView("persons/list");
        List<Person> persons = personRepository.findAll();
        modelAndView.addObject("persons", persons);
        return modelAndView;
    }

    @GetMapping(value = "/create")
    ModelAndView create() {
        log.info("Creating person");
        ModelAndView modelAndView = new ModelAndView("persons/create");
        modelAndView.addObject("personCommand", new PersonCommand());
        return modelAndView;
    }

    @PostMapping
    ModelAndView save(@Valid PersonCommand personCommand, BindingResult bindingResult) {
        log.info("Registering new Person: {}", personCommand.getNickname());
        ModelAndView modelAndView = new ModelAndView("persons/list");
        if (bindingResult.hasErrors()) {
            modelAndView.setViewName("persons/create");
            modelAndView.addObject("personCommand", personCommand);
            return modelAndView;
        }
        Person person = new Person(null, personCommand.getNickname(), personCommand.getEmail(), personCommand.getEin());
        personRepository.save(person);
        List<Person> persons = personRepository.findAll();
        modelAndView.addObject("persons", persons);
        return modelAndView;
    }

}
```

In this controller, using the `@Valid` annotation from `jakarta.validation` package, you can retrieve all the attributes from the form bound to the `PersonCommand` object. In the code, you check for errors, and if so, send the user back to the original form template. In that situation, all the error attributes are displayed. If all of the user’s attributes are valid, it redirects the browser to the list persons template; otherwise, it will show the form with the error messages.

```html
<!DOCTYPE html>
<html xmlns:th="https://www.thymeleaf.org">
<head>
    <title th:text="#{app.title}"/>
</head>
<body>
  <h1 th:text="#{person.register}" />
  <form id="create" th:action="@{/persons}" th:object="${personCommand}" method="post">
    <div class="form-group">
      <label class="col-sm-1 col-form-label-lg" for="nickname">Nickname:</label>
      <input class="form-control form-control-lg" type="text" name="nickname" th:field="*{nickname}" placeholder="nickname" id="nickname"/>
      <label th:if="${#fields.hasErrors('nickname')}" th:errors="*{nickname}"></label>
    </div>
    <br/>
    <div class="form-group">
      <label class="col-sm-1 col-form-label-lg" for="email">Email:</label>
      <input class="form-control form-control-lg" type="text" name="email" th:field="*{email}" placeholder="email" id="email"/>
      <label th:if="${#fields.hasErrors('email')}" th:errors="*{email}"></label>
    </div>
    <br/>
    <div class="form-group">
      <label class="col-sm-1 col-form-label-lg" for="ein">EIN:</label>
      <input class="form-control form-control-lg" type="text" name="ein" th:field="*{ein}" placeholder="EIN" id="ein"/>
      <label th:if="${#fields.hasErrors('ein')}" th:errors="*{ein}"></label>
    </div>
    <br/><br/>
    <button type="submit">Submit</button>
  </form>
  <footer>
      <a href="https://josdem.io/">josdem</a>
  </footer>
</body>
</html>
```

As you can see, the page contains a simple form. Using the Thymeleaf expression `"th:object="${personCommand}"`, it maps the person object with the form. This strategy is known as a "bean-backed" form. They are tagged as `th:field="{nickname}"`. Next to each field is a secondary element used to show any validation errors.

```java
package com.josdem.springboot.validation.command;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.Size;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class PersonCommand implements Command {

    @Size(min = 6, max = 50)
    private String nickname;

    @Email
    @Size(min = 6, max = 250)
    private String email;

    @Size(min = 9, max = 9)
    private String ein;

}
```

In the domain transfer object `PersonCommand`, we defined all desired field constraints, like minimum and maximum size, and email validation format using the `jakarta.validation` annotation. `Command` is just a Serializable interface.

```java
package com.josdem.springboot.validation.command;

import java.io.Serializable;

public interface Command extends Serializable {}
```

Here is the complete `build.gradle` file where you can see dependencies and configuration.

```groovy
plugins {
  id 'org.springframework.boot' version '3.3.2'
  id 'io.spring.dependency-management' version '1.1.6'
  id 'java'
}

group = 'com.josdem.springboot.validator'
version = '0.0.1-SNAPSHOT'

java {
  toolchain {
    languageVersion = JavaLanguageVersion.of(21)
  }
}

configurations {
  compileOnly {
    extendsFrom annotationProcessor
  }
}

repositories {
  mavenCentral()
}

dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-web'
  implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
  implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
  implementation 'org.springframework.boot:spring-boot-starter-validation'
  runtimeOnly 'com.mysql:mysql-connector-j'
  compileOnly 'org.projectlombok:lombok'
  annotationProcessor 'org.projectlombok:lombok'
  testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

To browse the project go [here](https://github.com/josdem/spring-boot-validation), to download the project:

```bash
git clone https://github.com/josdem/spring-boot-validation.git
```

To run the project:

```bash
./gradlew bootRun
```

To test the project:

```bash
./gradlew test
```

[Return to the main article](/techtalk/spring_boot)
