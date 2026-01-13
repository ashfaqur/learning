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

Bean: Object managed by Spring IoC container

Lifecycle: Instantiation → DI → Aware callbacks → PostConstruct → ready → PreDestroy

Scopes: Define lifetime & visibility

Singleton (default), Prototype, Request, Session, Application, WebSocket

Spring Boot auto-manages most lifecycles for you, but knowing this is important for stateful beans and testing.


