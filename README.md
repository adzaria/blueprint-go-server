# Clean GO architechture 🛡️

I believe post-OOP languages are doing great with ports & adapters from hexagonal architecture. There are a lot of resources online describing the core principles of hexagonal architecture, and a lot of ressources on how to implement it in Go. They are great (see the list bellow). But because they aim at senior developers, I feel they somehow fail at answering the most basic real life implementation questions: how the heck do I send emails ? Logs ? Where do I put my middlewares ? How do I make request to other services ?

This (unfinished) repo aim at answering this question: how to achieve buiding a stable, highly available, scalable REST API in go.

I have years of experience building NodeJS servers. I've put a lot of pain and suffering (for good) building/maintaining/refactoring them. Though I am rather new in Go. This is why I'm trying to involve more experienced developers to this project, and you should feel free to share your experience and participate in this project.

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

## About hexagonal architecture
- The original article from Alistair Cockburn: https://alistair.cockburn.us/hexagonal-architecture/

## How to implement it in Go
