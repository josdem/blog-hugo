+++
title =  "Spring Webflux Internationalization"
description = "Spring webflux internationalization"
date = 2024-07-25T11:12:08-04:00
tags = ["josdem", "techtalks","programming","technology","spring webflux"]
categories = ["techtalk", "code", "spring"]
+++

In this technical post, we will see how to use different languages (English and Spanish) in your Spring Webflux application along with [Thymeleaf](https://www.thymeleaf.org/) template or [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). **NOTE:** If you need to know what tools you need to have installed in your computer in order to create a Spring Boot basic project, please refer my previous post: [Spring Boot](/techtalk/spring_boot). Then, let's create a new project using this command:

```bash
spring init --dependencies=webflux --build=gradle --language=java spring-webflux-internationalization
```

This is the `build.gradle` generated file:

```groovy
plugins {
  id 'org.springframework.boot' version '2.1.7.RELEASE'
  id 'java'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.jos.dem.spring.webflux.internationalization'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
  mavenCentral()
}

dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-webflux'
  testImplementation 'org.springframework.boot:spring-boot-starter-test'
  testImplementation 'io.projectreactor:reactor-test'
}
```

Then add Thymeleaf dependency to your `build.gradle` file

```groovy
implementation('org.thymeleaf:thymeleaf-spring5:3.0.11.RELEASE')
```

Spring Boot has an strategy interface for resolving messages, with support for internationalization of such messages.

```java
@Bean
public MessageSource messageSource() {
  ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
  messageSource.setBasenames("i18n/messages");
  messageSource.setDefaultEncoding("UTF-8");
  return messageSource;
}
```

Now is time to configure template resolver, template engine and a view resolver since is what we need in order to configure Thymeleaf as html template.

```java
@Bean
public ITemplateResolver thymeleafTemplateResolver() {
  final SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
  resolver.setApplicationContext(this.context);
  resolver.setPrefix("classpath:templates/");
  resolver.setSuffix(".html");
  resolver.setTemplateMode(TemplateMode.HTML);
  resolver.setCacheable(false);
  resolver.setCheckExistence(false);
  return resolver;
}

@Bean
public ISpringWebFluxTemplateEngine thymeleafTemplateEngine() {
  SpringWebFluxTemplateEngine templateEngine = new SpringWebFluxTemplateEngine();
  templateEngine.setTemplateResolver(thymeleafTemplateResolver());
  return templateEngine;
}

@Bean
public ThymeleafReactiveViewResolver thymeleafReactiveViewResolver() {
  ThymeleafReactiveViewResolver viewResolver = new ThymeleafReactiveViewResolver();
  viewResolver.setTemplateEngine(thymeleafTemplateEngine());
  return viewResolver;
}

@Override
public void configureViewResolvers(ViewResolverRegistry registry) {
  registry.viewResolver(thymeleafReactiveViewResolver());
}
```

Here is the complete complete web configuration

```java
package com.jos.dem.spring.webflux.internationalization.config;

import org.springframework.context.MessageSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.reactive.config.EnableWebFlux;
import org.springframework.web.reactive.config.WebFluxConfigurer;
import org.springframework.context.support.ResourceBundleMessageSource;
import org.springframework.web.reactive.config.ViewResolverRegistry;

import org.thymeleaf.templatemode.TemplateMode;
import org.thymeleaf.templateresolver.ITemplateResolver;
import org.thymeleaf.spring5.SpringWebFluxTemplateEngine;
import org.thymeleaf.spring5.ISpringWebFluxTemplateEngine;
import org.thymeleaf.spring5.view.reactive.ThymeleafReactiveViewResolver;
import org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver;

@Configuration
@EnableWebFlux
public class WebConfig implements ApplicationContextAware, WebFluxConfigurer {

  private ApplicationContext context;

  @Override
  public void setApplicationContext(ApplicationContext context) {
    this.context = context;
  }

  @Bean
  public MessageSource messageSource() {
    ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
    messageSource.setBasenames("i18n/messages");
    messageSource.setDefaultEncoding("UTF-8");
    return messageSource;
  }

  @Bean
  public ITemplateResolver thymeleafTemplateResolver() {
    final SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
    resolver.setApplicationContext(this.context);
    resolver.setPrefix("classpath:templates/");
    resolver.setSuffix(".html");
    resolver.setTemplateMode(TemplateMode.HTML);
    resolver.setCacheable(false);
    resolver.setCheckExistence(false);
    return resolver;
  }

  @Bean
  public ISpringWebFluxTemplateEngine thymeleafTemplateEngine() {
    SpringWebFluxTemplateEngine templateEngine = new SpringWebFluxTemplateEngine();
    templateEngine.setTemplateResolver(thymeleafTemplateResolver());
    return templateEngine;
  }

  @Bean
  public ThymeleafReactiveViewResolver thymeleafReactiveViewResolver() {
    ThymeleafReactiveViewResolver viewResolver = new ThymeleafReactiveViewResolver();
    viewResolver.setTemplateEngine(thymeleafTemplateEngine());
    return viewResolver;
  }

  @Override
  public void configureViewResolvers(ViewResolverRegistry registry) {
    registry.viewResolver(thymeleafReactiveViewResolver());
  }

}
```

As you can see `SpringResourceTemplateResolver` is in charge to set our template files path and extension. In this case will will define an `index.html` web page with a hello world message.

```html
<html>
  <head>
    <meta charset="utf-8">
    <title>Internationalization with Spring Webflux</title>
  </head>
  <body>
  	<p th:text="#{user.hello}"></p>
  </body>
</html>
```

Also `ResourceBundleMessageSource` is defining message resources path for each supported language. In this case we will define `messages.properties` for English.

```properties
user.hello=Hello from internationalization!
```

And `messages_es.properties` for Spanish

```properties
user.hello=¡Hola Internacionalización!
```

Next step is to tell Spring to use a Locale resolver. So we need to add a configuration class which extends from `DelegatingWebFluxConfiguration` and returns our custom `LocaleContextResolver`.

```java
package com.jos.dem.spring.webflux.internationalization.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.server.i18n.LocaleContextResolver;
import org.springframework.web.reactive.config.DelegatingWebFluxConfiguration;

import com.jos.dem.spring.webflux.internationalization.helper.LocaleResolver;

@Configuration
public class LocaleSupportConfig extends DelegatingWebFluxConfiguration {

  @Override
  protected LocaleContextResolver createLocaleContextResolver() {
    return new LocaleResolver();
  }

}
```

Here is our locale resolver

```java
package com.jos.dem.spring.webflux.internationalization.helper;

import java.util.List;
import java.util.Locale;

import org.springframework.web.server.ServerWebExchange;
import org.springframework.web.server.i18n.LocaleContextResolver;
import org.springframework.context.i18n.LocaleContext;
import org.springframework.context.i18n.SimpleLocaleContext;

public class LocaleResolver implements LocaleContextResolver {

  @Override
  public LocaleContext resolveLocaleContext(ServerWebExchange exchange) {
    String language = exchange.getRequest().getHeaders().getFirst("Accept-Language");

    Locale targetLocale = Locale.getDefault();
    if (language != null && !language.isEmpty()) {
      targetLocale = Locale.forLanguageTag(language);
    }
    return new SimpleLocaleContext(targetLocale);
  }

  @Override
  public void setLocaleContext(ServerWebExchange exchange, LocaleContext localeContext) {
    throw new UnsupportedOperationException("Not Supported");
  }

}
```

That's it, we are reading language support in the headers from client request, in that way we can show the right message to our client whether is using English or Spanish. Finally here is our controller to render the index web page.

```java
package com.jos.dem.spring.webflux.internationalization.handler;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class InternationalizationController {

  @GetMapping("/")
  public String index() {
    return "index";
  }

}
```

To run the project:

```bash
gradle bootRun
```

Then, execute this command:

```bash
curl -v http://localhost:8080
```

And you sould see this output:

```bash
* Rebuilt URL to: http://localhost:8080/
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET / HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: text/html
< Content-Language: en-US
< content-length: 183
<
<html>
  <head>
    <meta charset="utf-8">
    <title>Internationalization with Spring Webflux</title>
  </head>
  <body>
  	<p>Hello from internationalization!</p>
  </body>
</html>
* Connection #0 to host localhost left intact
```

To browse the project go [here](https://github.com/josdem/spring-webflux-internationalization), to download the project:

```bash
git clone git@github.com:josdem/spring-boot-internationalization.git
git fetch
git checkout thymeleaf
```

## REST internationalization

If you need to use internationalization in a REST api instead of Thymeleaf, please consider this following changes.

```java
package com.jos.dem.spring.webflux.internationalization.handler;

import org.springframework.web.server.ServerWebExchange;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.beans.factory.annotation.Autowired;

import com.jos.dem.spring.webflux.internationalization.service.LocaleService;

@RestController
public class InternationalizationController {

  @Autowired
  private LocaleService localeService;

  @GetMapping("/")
  public String index(ServerWebExchange exchange) {
    return localeService.getMessage("user.hello", exchange);
  }

}
```

Now we are using `@RestController` instead of `@Controller` and a locale service to resolve our messages.

```java
package com.jos.dem.spring.webflux.internationalization.service;

import org.springframework.web.server.ServerWebExchange;

public interface LocaleService {
  String getMessage(String code, ServerWebExchange exchange);
}
```

Here is the implementation

```java
package com.jos.dem.spring.webflux.internationalization.service.impl;

import org.springframework.stereotype.Service;
import org.springframework.context.MessageSource;
import org.springframework.context.i18n.LocaleContext;
import org.springframework.web.server.ServerWebExchange;
import org.springframework.beans.factory.annotation.Autowired;

import com.jos.dem.spring.webflux.internationalization.service.LocaleService;
import com.jos.dem.spring.webflux.internationalization.helper.LocaleResolver;

@Service
public class LocaleServiceImpl implements LocaleService {

  @Autowired
  private MessageSource messageSource;
  @Autowired
  private LocaleResolver localeResolver;

  @Override
  public String getMessage(String code, ServerWebExchange exchange) {
    LocaleContext localeContext = localeResolver.resolveLocaleContext(exchange);
    return messageSource.getMessage(code, null, localeContext.getLocale());
  }

}
```

That's it, we are using the same locale resolver as Thymeleaf but now is a `@Component` so we can use `@Autowired` to inject it

```java
package com.jos.dem.spring.webflux.internationalization.helper;

import java.util.List;
import java.util.Locale;

import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import org.springframework.web.server.i18n.LocaleContextResolver;
import org.springframework.context.i18n.LocaleContext;
import org.springframework.context.i18n.SimpleLocaleContext;

@Component
public class LocaleResolver implements LocaleContextResolver {

  @Override
  public LocaleContext resolveLocaleContext(ServerWebExchange exchange) {
    String language = exchange.getRequest().getHeaders().getFirst("Accept-Language");

    Locale targetLocale = Locale.getDefault();
    if (language != null && !language.isEmpty()) {
      targetLocale = Locale.forLanguageTag(language);
    }
    return new SimpleLocaleContext(targetLocale);
  }

  @Override
  public void setLocaleContext(ServerWebExchange exchange, LocaleContext localeContext) {
    throw new UnsupportedOperationException("Not Supported");
  }

}
```

To run the project:

```bash
gradle bootRun
```

Then, execute this command:

```bash
curl -v http://localhost:8080
```

And you sould see this output:

```bash
* Rebuilt URL to: http://localhost:8080/
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET / HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: text/plain;charset=UTF-8
< Content-Length: 32
<
* Connection #0 to host localhost left intact
Hello from internationalization!%
```

To browse the project go [here](https://github.com/josdem/spring-webflux-internationalization), to download the project:

```bash
git clone git@github.com:josdem/spring-boot-internationalization.git
git fetch
git checkout rest
```

[Return to the main article](/techtalk/spring_boot)
