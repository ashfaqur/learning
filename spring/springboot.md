# SpringBoot

- [SpringBoot](#springboot)
- [History](#history)
  - [Why Java EE / Servlet / EJB frameworks were painful](#why-java-ee--servlet--ejb-frameworks-were-painful)
  - [What is Spring?](#what-is-spring)
  - [What Problems Did Spring Solve?](#what-problems-did-spring-solve)
- [Intro](#intro)
  - [What is Hibernate?](#what-is-hibernate)
  - [Inversion of Control (IoC)](#inversion-of-control-ioc)
  - [Dependency Injection (DI)](#dependency-injection-di)
  - [POJO-based programming philosophy](#pojo-based-programming-philosophy)
  - [Spring](#spring)
  - [Spring Boot](#spring-boot)
- [Spring Core - Beans](#spring-core---beans)
  - [Bean lifecycle \& scopes](#bean-lifecycle--scopes)
    - [Bean lifecycle](#bean-lifecycle)
    - [Bean scopes](#bean-scopes)
  - [Constructor vs field vs setter injection](#constructor-vs-field-vs-setter-injection)
  - [@Component / @Service / @Repository](#component--service--repository)
  - [@Configuration / @Bean](#configuration--bean)
  - [Profiles \& Environment Configuration](#profiles--environment-configuration)
  - [Auto-Configuration \& Starters (Spring Boot Core)](#auto-configuration--starters-spring-boot-core)
  - [Spring Boot Starters](#spring-boot-starters)
  - [application.properties / YAML](#applicationproperties--yaml)
  - [Profiles](#profiles)
  - [Externalized Configuration](#externalized-configuration)


# History

## Why Java EE / Servlet / EJB frameworks were painful

Early Java EE & EJB systems were painful because they were:
- overly complex and configuration-heavy
- tightly coupled to heavy application servers
- slow to develop, deploy, and test
- full of boilerplate and container magic
- designed for unnecessary distributed systems

Spring was created to:
- simplify enterprise Java development
- make code testable and lightweight
- replace EJBs with POJO-based architecture
- give developers control without complexity

Spring Boot later made Spring:
- easier to configure
- faster to start
- simpler to deploy

## What is Spring?

A lightweight, modular Java framework that:
- manages object lifecycle via IoC & DI
- enables loosely-coupled, testable design
- provides tools for web, data, security, integration
- lets you write business logic using simple POJOs

## What Problems Did Spring Solve?

Spring addressed pain points of early Java EE:
- tight coupling & rigid architecture
- heavy containers & slow deployment
- excessive boilerplate & XML configuration
- code that was hard to test
- duplicated cross-cutting concerns

It solved them using:
- Dependency Injection (loose coupling)
- POJO-based design
- AOP for cross-cutting behavior
- declarative transactions & security
- lightweight runtime

# Intro

## What is Hibernate?

Hibernate is a Java framework that maps Java objects (POJOs) to database tables. It allows you to:
- Persist Java objects to relational databases without writing SQL manually
- Retrieve database records as Java objects
- Manage relationships between objects (one-to-many, many-to-many, etc.)
- Handles CRUD, relationships, transactions, caching

Core role: solve the object-relational impedance mismatch

With Spring Boot, Hibernate usually runs under the hood via JPA

## Inversion of Control (IoC)

The framework (Spring) controls:
- object creation
- wiring
- lifecycle

instead of application code doing it manually.

## Dependency Injection (DI)

The mechanism that delivers IoC by:
- injecting required dependencies into a class
- instead of the class creating them itself

Spring implements IoC via DI using:
- constructor injection (recommended)
- setter injection
- (legacy) field injection

Benefits:
- loose coupling
- easier testing & mocking
- better maintainability & modular design

## POJO-based programming philosophy

Spring encourages writing:
- plain Java classes
- with no framework inheritance or container dependencies

Spring provides infrastructure behavior via:
- Dependency Injection
- AOP proxies
- metadata annotations

Benefits:
- highly testable
- loosely coupled
- clean separation of concerns
- business logic remains framework-independent

This philosophy was a major improvement over heavy, container-bound EJB architectures.

## Spring

A lightweight, modular Java framework that manages application components (beans) via IoC/DI, supports AOP, transactions, security, and data access. Focuses on POJO-based, loosely coupled, testable applications.

## Spring Boot

A developer productivity layer on top of Spring that provides auto-configuration, opinionated defaults, embedded servers, and starters, enabling you to build Spring applications quickly with minimal boilerplate.

What Spring Boot changed
- From → manual configuration everywhere
- To → auto-configuration + sensible defaults

Key innovations:
- auto-config based on classpath & context
- opinionated defaults (but overridable)
- dependency “starters”
- embedded servers (run as standalone apps)
- production features out of the box

Spring Boot made Spring apps faster to build, easier to maintain, and better suited for microservices & modern deployment.

# Spring Core - Beans

## Bean lifecycle & scopes

- Bean: An object created, managed, and destroyed by the Spring IoC container.
- Spring controls when the bean is created, how dependencies are injected, and when it is cleaned up.

### Bean lifecycle

Lifecycle describes the steps a bean goes through from creation to destruction.

- Instantiation  
  - Spring creates the bean instance (using constructor or factory method)
  - At this point, the object exists but has no dependencies yet

- Dependency Injection (DI)  
  - Spring injects required dependencies (constructor, setter, or field)
  - Bean is now fully wired but not yet ready for use

- Aware callbacks  
  - If the bean implements *Aware interfaces, Spring injects container-related info
  - Examples: BeanNameAware, ApplicationContextAware
  - Used when a bean needs access to Spring infrastructure

- PostConstruct  
  - Method annotated with @PostConstruct is called
  - Used for initialization logic (validation, setup, loading data)
  - Bean is now fully initialized

- Ready  
  - Bean is available for use by the application
  - Business logic can safely run here

- PreDestroy  
  - Method annotated with @PreDestroy is called before bean removal
  - Used for cleanup (closing connections, releasing resources)
  - Only applies to beans managed by the container (not prototype beans)

### Bean scopes

Scopes define how long a bean lives and how it is shared.

- Singleton (default)  
  - One instance per Spring container
  - Shared across the entire application
  - Created at startup (usually)
  - Best for stateless services

- Prototype  
  - New instance every time the bean is requested
  - Spring does not manage full lifecycle (no PreDestroy)
  - Use carefully, mainly for stateful objects

- Request  
  - One instance per HTTP request
  - Exists only during a single web request
  - Used in web applications

- Session  
  - One instance per HTTP session
  - Lives as long as the user session
  - Useful for user-specific state

- Application  
  - One instance per ServletContext
  - Shared across the entire web application
  - Similar to singleton but scoped to the web context

- WebSocket  
  - One instance per WebSocket session
  - Used in real-time, bidirectional communication

Rule of thumb
- Use singleton for most beans
- Avoid state in singleton beans
- Use web scopes only when building web-layer logic
- Prefer stateless, immutable beans

## Constructor vs field vs setter injection

Dependency Injection (DI) is how Spring provides required dependencies to a bean.

1. Constructor Injection (recommended)
- Dependencies are provided through the constructor
- Makes dependencies mandatory
- Allows use of final fields (immutability)
- Fails fast at application startup if dependency is missing
- Easy to unit test without Spring context
- @Autowired is optional if there is only one constructor

Best practice in modern Spring applications.

```java

@Service
public class PaymentService {
    public void processPayment() {
        System.out.println("Payment processed");
    }
}

@Service
public class OrderService {

    private final PaymentService paymentService;

    // @Autowired optional if only one constructor
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public void placeOrder() {
        paymentService.processPayment();
    }
}
```

2. Field Injection (discouraged)
- Dependencies injected directly into fields using @Autowired
- Hidden dependencies (not visible in constructor)
- Harder to unit test (requires Spring or reflection)
- Cannot use final fields
- Tightly couples class to Spring framework

```java
@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;

    public void placeOrder() {
        paymentService.processPayment();
    }
}
```

3. Setter Injection (situational)
- Dependencies injected through setter methods
- Suitable for optional dependencies
- Allows changing dependency after object creation
- Object may exist in partially initialized state
- Less safe than constructor injection

Use only when dependency is optional.

```java
@Service
public class OrderService {

    private PaymentService paymentService;

    @Autowired(required = false)
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public void placeOrder() {
        if (paymentService != null) {
            paymentService.processPayment();
        }
    }
}
```

Rule of Thumb
- Prefer constructor injection
- Use setter injection for optional dependencies
- Avoid field injection

## @Component / @Service / @Repository

- These annotations mark classes as Spring-managed beans.
- They enable component scanning so Spring can detect and register them in the IoC container.

1. @Component
- Generic stereotype annotation
- Marks a class as a Spring bean
- Used when the class does not fit into a specific layer
- Base annotation for other stereotypes

```java
@Component
public class MyUtility { }
```

2. @Service
- Specialized form of `@Component`
- Used for service layer classes
- Represents business logic
- Improves readability and architectural clarity

```java
@Service
public class EmployeeService { }
```

No technical difference from @Component, but semantically clearer.

3. @Repository
- Specialized form of `@Component`
- Used for data access layer (DAO / repository classes)
- Indicates interaction with the database
- Enables automatic exception translation (converts low-level persistence exceptions into Spring’s DataAccessException)

```java
@Repository
public class EmployeeRepository { }
```

Key Differences
- All three are detected via component scanning
- `@Service` and `@Repository` are semantic specializations of `@Component`
- `@Repository` adds persistence exception translation
- Used to clearly separate application layers

Rule of Thumb
- Use @Controller or @RestController for web layer
- Use @Service for business logic
- Use @Repository for database access
- Use @Component for general-purpose beans

## @Configuration / @Bean

Used for explicit bean definitions and Java-based configuration.

1. `@Configuration`
- Marks a class as a source of bean definitions
- Tells Spring that this class contains configuration logic
- Acts like a replacement for old XML configuration
- Classes annotated with `@Configuration` are processed by Spring at startup
- Often used for infrastructure setup (security, custom beans, third-party integrations)

```java
@Configuration
public class AppConfig { }
```

2. `@Bean`
- Used inside a `@Configuration` class
- Marks a method as a bean producer
- The method return value is registered as a Spring bean
- Bean name defaults to the method name (unless specified)

```java
@Configuration
public class AppConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```
Spring registers a bean named "passwordEncoder".

When to use `@Bean` instead of `@Component`

Use `@Bean` when:
- You are configuring third-party classes (cannot add annotations to them)
- You need custom initialization logic
- You want fine-grained control over bean creation

```java
@Bean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

Technical Detail
- `@Configuration` classes are enhanced by Spring using proxies
- Ensures each `@Bean` method returns the same singleton instance by default
- Prevents multiple instantiations when calling bean methods internally

`@Component` vs `@Bean`

`@Component`
- Used on classes you control
- Detected via component scanning
- Simpler and automatic

`@Bean`
- Used inside `@Configuration` classes
- Explicit bean definition
- More control over creation

Rule of Thumb
- Use `@Component` / `@Service` / `@Repository` for your own classes
- Use `@Bean` for external libraries or complex configuration
- Keep configuration separate from business logic


## Profiles & Environment Configuration

Used to define different configurations for different environments (dev, test, prod, etc.).

Spring Profiles allow you to activate specific beans or configuration depending on the runtime environment.

1. `@Profile`
- Used to mark a bean or configuration as active only in specific environments
- Bean is created only if the profile is active
- Helps separate development, testing, and production settings
- Prevents environment-specific logic from being hardcoded

```java
@Configuration
@Profile("dev")
public class DevConfig {

    @Bean
    public DataSource dataSource() {
        return new H2DataSource();
    }
}
```

This configuration is only loaded when the `dev` profile is active.

You can also use it on components:

```java
@Service
@Profile("prod")
public class PaymentServiceImpl implements PaymentService { }
```

2. Activating Profiles

Profiles can be activated in several ways.
- Using application.properties: spring.profiles.active=dev
- Using command line: --spring.profiles.active=prod
- Using environment variable: SPRING_PROFILES_ACTIVE=prod

3. Profile-specific configuration files

Spring Boot supports profile-specific configuration files.
- application.properties
- application-dev.properties
- application-prod.properties

If `dev` profile is active, Spring loads:
- application.properties
- then overrides with application-dev.properties

This allows environment-specific overrides.

4. Environment Configuration
Spring Boot externalizes configuration.
Common sources (in order of precedence):
- Command line arguments
- Environment variables
- application.properties / application.yml
- Default values in code

Example:

server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/app

Configuration values can be injected using:

```java
@Value("${server.port}")
private int port;
```

Or using `@ConfigurationProperties` (recommended for grouped settings).

5. Why Profiles Matter

- Separate dev/test/prod behavior cleanly
- Avoid manual code changes between environments
- Keep production secrets out of source code
- Support cloud and container deployments easily

Rule of Thumb

- Always use profiles for environment-specific behavior
- Never hardcode credentials or URLs
- Use profile-specific property files for clean separation
- Prefer `@ConfigurationProperties` over scattered `@Value` injections


## Auto-Configuration & Starters (Spring Boot Core)

Spring Boot’s core innovation is auto-configuration.
It automatically configures application based on the dependencies on the classpath and existing beans.

1. What is Auto-Configuration
- Automatically configures Spring beans based on:
  - Classpath dependencies
  - Existing beans
  - Configuration properties
- Reduces manual configuration
- Provides sensible defaults
- Still fully overridable

Example:
If spring-boot-starter-web is on the classpath:
- Spring Boot automatically configures:
  - Embedded Tomcat
  - DispatcherServlet
  - Jackson (JSON)
  - Basic Spring MVC setup

2. How Auto-Configuration Works

Spring Boot uses:
- @EnableAutoConfiguration (included in @SpringBootApplication)
- Conditional annotations
- Classpath detection

@SpringBootApplication includes:
- @Configuration
- @EnableAutoConfiguration
- @ComponentScan

Auto-configuration classes are loaded based on conditions like:
- @ConditionalOnClass
- @ConditionalOnMissingBean
- @ConditionalOnProperty

Example logic (simplified):

If DataSource class is present
AND no DataSource bean exists
THEN create a default DataSource bean

This is how Boot “decides” what to configure.

3. Starters
- Starters are curated dependency bundles.
- Instead of manually adding multiple dependencies, add one starter.

Example:
spring-boot-starter-web includes:
- Spring MVC
- Jackson
- Validation
- Embedded Tomcat
- Logging

spring-boot-starter-data-jpa includes:
- Spring Data JPA
- Hibernate
- Transaction support

Starters:
- Simplify dependency management
- Ensure compatible versions
- Reduce configuration errors

4. Overriding Auto-Configuration
Override auto-config by:
- Defining own bean
- Setting properties in application.properties
- Excluding specific auto-config classes

Example:

spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration

Or:

@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)

If you define your own DataSource bean, Boot backs off automatically.

5. Why Auto-Configuration Matters
- Eliminates boilerplate setup
- Speeds up development
- Standardizes configuration
- Enables convention over configuration
- Makes microservices quick to build

Rule of Thumb
- Let Spring Boot auto-configure by default
- Override only when necessary
- Avoid fighting the framework
- Understand what is auto-configured to debug issues effectively

## Spring Boot Starters

Starters are curated dependency bundles that simplify dependency management.

Instead of manually adding multiple related libraries, add a single starter.

1. What is a Starter
- A starter is a pre-configured dependency descriptor
- Pulls in a set of compatible libraries
- Ensures version alignment
- Reduces configuration and dependency conflicts

Example:

spring-boot-starter-web

Instead of manually adding:
- Spring MVC
- Jackson
- Validation
- Embedded Tomcat
- Logging

Add one dependency and get all of them.

2. Common Starters

spring-boot-starter-web
- Spring MVC
- Jackson (JSON)
- Embedded Tomcat
- Validation

spring-boot-starter-data-jpa
- Spring Data JPA
- Hibernate
- Transaction management

spring-boot-starter-security
- Spring Security core
- Authentication & authorization support

spring-boot-starter-test
- JUnit
- Mockito
- Spring Test
- Assertion libraries

spring-boot-starter-actuator
- Production-ready monitoring
- Health checks
- Metrics
- Application info

3. Why Starters Matter

- Simplify dependency management
- Prevent version mismatch issues
- Provide opinionated defaults
- Accelerate development
- Improve consistency across projects

4. How Starters Work with Auto-Configuration

Starters only provide dependencies.

Auto-configuration uses those dependencies to configure beans automatically.

Example:

Add spring-boot-starter-web
→ Boot detects Spring MVC on classpath
→ Auto-configures DispatcherServlet, Tomcat, JSON support

Starters + Auto-Configuration = Minimal setup, maximum functionality

5. Custom Starters

Organizations can create internal starters to:
- Standardize logging
- Enforce security setup
- Preconfigure monitoring
- Apply company-wide conventions

Rule of Thumb

- Use official starters whenever possible
- Avoid manually adding overlapping dependencies
- Let Boot manage versions through starters
- Understand what each starter brings into your project

## application.properties / YAML

Spring Boot uses external configuration files to define application settings.

These files allow you to configure behavior without changing code.

1. application.properties

- Uses key=value format
- Simple and flat structure
- Easy to read for small configs
- Most commonly used format

Example:

server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/app
spring.datasource.username=root
spring.datasource.password=secret

2. application.yml (YAML)

- Uses indentation-based hierarchical structure
- Cleaner for complex or nested configuration
- Avoids repetition of long prefixes
- More readable for grouped properties

Equivalent YAML example:

```yml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/app
    username: root
    password: secret
```

3. Differences Between properties and YAML

application.properties
- Flat structure
- Repeats full property paths
- Familiar key=value syntax

application.yml
- Hierarchical
- Cleaner for large configs
- Sensitive to indentation
- Uses spaces, not tabs

Both formats are fully supported by Spring Boot.

4. How Configuration is Loaded

Spring Boot loads configuration in this order (higher overrides lower):
- Command line arguments
- Environment variables
- application.properties / application.yml
- Profile-specific files (application-dev.yml, etc.)
- Default values in code

Example profile file:

application-dev.yml

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/devdb

5. Injecting Configuration Values

Using @Value:

```java
@Value("${server.port}")
private int port;

Using @ConfigurationProperties (recommended for grouped settings):

@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private String version;
}

```

Rule of Thumb
- Use application.yml for complex or hierarchical configuration
- Use properties for simple setups
- Prefer @ConfigurationProperties for structured configuration
- Never store secrets directly in source-controlled config files

## Profiles

Spring Profiles allow to define environment-specific configuration and beans.

They help separate development, testing, and production behavior without changing code.

1. What is a Profile
- A named environment (e.g., dev, test, prod)
- Controls which beans and configurations are active
- Prevents mixing environment-specific logic
- Commonly used for databases, logging, security settings

2. Defining Profile-Specific Beans

Use @Profile to restrict a bean or configuration to a specific environment.

```java
@Configuration
@Profile("dev")
public class DevConfig {

    @Bean
    public DataSource dataSource() {
        return new H2DataSource();
    }
}
```

This bean is created only when the "dev" profile is active.

You can also use it on components:

```java
@Service
@Profile("prod")
public class PaymentServiceImpl implements PaymentService { }
```

3. Activating Profiles

Profiles can be activated in several ways.

- In application.properties:
spring.profiles.active=dev

- Using command line:
--spring.profiles.active=prod

- Using environment variable:
SPRING_PROFILES_ACTIVE=prod

4. Profile-Specific Configuration Files

Spring Boot supports profile-specific config files.

Examples:
- application.properties
- application-dev.properties
- application-prod.yml

If the "dev" profile is active:
- application.properties is loaded first
- application-dev.properties overrides matching values

5. Multiple Profiles

You can activate multiple profiles:

spring.profiles.active=dev,cloud

Beans annotated with either profile will be loaded.

6. Why Profiles Are Important
- Clean separation of environments
- Different databases per environment
- Different logging levels
- Safe production configuration
- Cloud/container deployment support

Rule of Thumb

- Always use profiles for environment differences
- Never hardcode environment-specific values
- Keep production credentials outside version control
- Use profile-specific files for clean overrides

## Externalized Configuration

Externalized configuration means separating configuration from application code.

Spring Boot allows to define settings outside the compiled application, so behavior can change without modifying code.

1. Why Externalized Configuration Matters
- Avoid hardcoding values (URLs, ports, credentials)
- Support different environments (dev, test, prod)
- Improve security (secrets not stored in code)
- Enable flexible deployments (cloud, containers, CI/CD)

2. Common Configuration Sources

Spring Boot reads configuration from multiple sources (higher priority overrides lower):

- Command line arguments
- Environment variables
- application.properties / application.yml
- Profile-specific config files
- Default values in code

Example:

server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/app

3. Injecting Configuration Values

Using @Value:

```java
@Value("${server.port}")
private int port;
```

Using @ConfigurationProperties (recommended for grouped properties):

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private String version;
}
```
4. Environment Variables

Spring Boot maps environment variables to properties.

Example:

SERVER_PORT=8082
SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/proddb

Environment variables override application.properties.

5. Profile-Specific Configuration

You can define environment-specific configuration files:

application-dev.yml
application-prod.yml

Activate profile:

spring.profiles.active=dev

Profile config overrides base config.

6. Configuration Hierarchy Concept

Spring resolves configuration by:

- Loading all sources
- Applying priority rules
- Overriding lower-priority values

This enables flexible deployment without code changes.

Rule of Thumb
- Never hardcode environment-specific values
- Prefer external configuration over code-based constants
- Use @ConfigurationProperties for structured settings
- Store secrets securely (env vars, vault, secret manager)
