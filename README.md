# Clean GO architechture 🛡️

Post-OOP languages are doing great implementing ports and adapters in an hexagonal architecture. There are a lot of resources online describing the core principles of hexagonal architecture, and a lot of ressources on how to implement it in Go. They are great. But because they aim at senior developers, I feel they somehow fail at answering the most basic real-life implementation questions: how the heck do I send emails ? Logs ? Where do I put my middlewares ? How do I make request to other services ? How do I implement a queue ?

This repo aims at answering this question: how to achieve buiding a stable, flexible, scalable, highly available REST API in Go.

I have years of experience building NodeJS servers. I've put a lot of pain and suffering (for good) building/maintaining/refactoring them. Though I am rather new in Go. This is why I'm trying to involve more experienced developers to this project, and you should feel free to share your experience and participate in this project.

## Why Go, and why hexagonal ?

### Why choosing Go

According to Go's FAQ, "One had to choose either efficient compilation, efficient execution, or ease of programming; all three were not available in the same mainstream language. [...] Go addressed these issues [...]. It also aimed to be modern, with support for networked and multicore computing".

In my humble opinion they nailed it.

Go maximizes ease to code, efficiency and low compile time. Additionaly one can feel it really was written for the web. Also the standard library is so good it makes using third party libraries useless in most cases, allowing you to achieve next-to-zero dependencies.

In short this is a first class language to pick when building a server.

### Why choosing hexagonal

## Ports and Adapters

## The folder structure

## Responsibility for each layer

## Use a registry to  connect ports and adapters together

## Create middlewares

## Connect a database

## Connect a new secondary actor

## Unitests

## How to create a new service

## How to create a new use case ? endpoint + handler

## Pub/Sub Workers

## Queues and workers

## Chron jobs

## Secrets

## Docker

## External ressources

- The original article about hexaagonal architectur from Alistair Cockburn: https://alistair.cockburn.us/hexagonal-architecture/
- The Twelve-Factor App: https://12factor.net/
- Go standards on how to structure a project: https://github.com/golang-standards/project-layout
- A clean implementation in Go by Matías Varela: https://medium.com/@matiasvarela/hexagonal-architecture-in-go-cfd4e436faa3
