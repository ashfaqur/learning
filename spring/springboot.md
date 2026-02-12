# SpringBoot

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

Persist Java objects to relational databases without writing SQL manually

Retrieve database records as Java objects

Manage relationships between objects (one-to-many, many-to-many, etc.)

Handles CRUD, relationships, transactions, caching

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

Bean: An object created, managed, and destroyed by the Spring IoC container. Spring controls when the bean is created, how dependencies are injected, and when it is cleaned up.

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

### Rule of thumb
- Use singleton for most beans
- Avoid state in singleton beans
- Use web scopes only when building web-layer logic
- Prefer stateless, immutable beans

## Constructor vs field vs setter injection

