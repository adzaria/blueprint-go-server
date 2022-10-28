```
This repo is a work in progress - last edit 27/10/2022
```

I feel post-OOP languages are doing great implementing ports and adapters in an Hexagonal Architecture. In this article I want to give it a try. I want to tackle some issues I've had implementing it, and to highlight some parts that I feel are missing in most articles I found. 

I have a few years working with servers in NodeJS. I've put a lot of energy into building/maintaining/massively refactoring them. Though I am rather new in Go. For this reason you should 1/ be caeful about what you read here and 2/ share your knowledge and participate.

This article uses the words "Clean architecture", "Hexagonal architecture" and "Ports & Adapters pattern" as synonyms.

# Clean go architecture 🛡️

There are a lot of resources online describing the core principles of Hexagonal Architecture, and a lot of ressources on how to implement it in Go. They are great. But because they aim at senior developers, I feel they somehow fail at answering the most basic real-life implementation questions: how the heck do I send emails ? Where do I put my middlewares ? How do I make requests to other services ? How do I implement a queue ? Should I turn everything into an Actor ? Or is it ok to import librairies ?

This repo aims at answering those questions questions and to achieve buiding a stable, flexible, scalable, highly available REST API in Go. It won't be achieved on the first shot.

This repo is a stanalone server, it asumes it exists behind a gateway.

# Why choosing Go ❓

According to Go's FAQ, "[Before Go], one had to choose either efficient compilation, efficient execution, or ease of programming; all three were not available in the same mainstream language. [...] Go addressed these issues [...]. It also aimed to be modern, with support for networked and multicore computing".

It's safe to say they did a great job at it. We can also mention the standard library is very good (including the http library) and easy to extend.

In short, Go is a first class language to pick when building a server.

# Why choosing hexagonal ❓

According to it's author, the ports and adapters architecture exists to inforce isolation of the application's components by design, so they can be developed and tested in isolation from its run-time devices and databases. Easy.

- It helps you focus on the business rules,
- It helps you to build a technology-agnostic application,
- It allows you to run tests in isolation,
- It clarifies who does what (each component have a well defined perimeter).

On the 'bad side' it surely is more technical than other patterns.

# Ports and Adapters architecture 🔌

So this architecture is about isolating the core business.

To achieve isolation, everything that is not pure business logic, types or models, and is either using the application (a Primary Actor, driving the application) or used by the application (a secondary actor, driven by the application) is just removed from the core and pushed away to the surounding Framework layer. 

Then, all communication between those actors and the application is achieved through Ports and Adapters.

Ports are exposed by the core. Adapters either call the core through actions defined by the ports (Primary adapters), or implement actions defined by the port (Secondary adapters).

If you already read about this pattern, chances are you came accross a diagram like this:

![fig1](./README/hexagonal_traditional_layers.png "fig1")

Notice how:
- Everything that is not part of the core business, types or models is pushed outside,
- All actors are all equally treated,
- The core do not adapt to actors, actors adapt to the core.

It is worth to mention the name hexagon comes from the idea that each side should expose a port. In real life though this would most likely be an n-gon, since you will probably never have exactly 6 ports... 

## ☠️ Responsibilities of each layer ☠️

"Entities" together with "Use cases" define the core of the application.

### The "Entities" layer 🧅

The "Entities" layer (or Domain layer) contains all the knowledge of the business (models, important types) and it can contain functions (for validation and methods associated to the structs).
// todo: verify when it's ok to have a function there
It can never import anything from any other layer, but it will be imported from other layers.

### The "Use-Cases" layer 🧅

#### The "Use-Cases" layer contains:
- The business logic acceesed by Primary actors and it's ports.
- Ports for secondary actors to implement.

#### Regarding it's implementation:
- Each service is a struct type, defining it's dependencies.
- Each service implements an interface (port) that defines actions exposed by the service.
- Each action exposed by the service is a method defined on the struct type.
- Each use-case (or action) should just return data or errors. It does not return a status code or headers.
- Each secondary actor is called using the injected dependency. 
- Secondary ports are interfaces defining actions needed by the core.

#### Regarding it's responsibilities:
- A service never receives the original http/gRpc request.

```
// todo: add an examples
```

### The "Interface" layer 🧅

#### The interface layer contains:
Adapters for Primary actors:
- Handlers that receives http / gRpc requests from Primary Actors
- Middlewares
- DTOs

Adapters for Secondary actors:
- Adapters implementing interfaces for Secondary Ports

#### Regarding it's implementation:
- A handler is a struct type, defining it's dependencies (services it needs to call).
- Each method defined on the struct is an adapter.

// todo, verify this> Note: I think a handler could techincally be allowed to import a service directly, since it depends on it and the service is from a lower layer. But it has a struct for anything else coming from the above layer already. For this reason and to keep ocnsistency I think injecting the service should be prefered.

### Regarding it's responsibilities:
- It can not contain any business logic.
- A Primary Adapter handles the request. It can choose an error statusses, parse the request to extract what is needed by the service. (I think it should not perform any kind of validation, the service should)
- Regarding a Primary Adapter: it receives data from the Actor, it calls the service, it sends an answer back,
- Regarding a Secondary Adapter: it receives data from the service, calls the Driven Actor, sends the data back to the service.

### The Drivers & Frameworks layer 🧅
The "Drivers & Frameworks" layer contains the "implementation details" we got rid of from the core. It has the database object, web frameworks, logger... Everything that would polute our tests, have side effects or perform api calls goes there.

## Dependency injection 💉

This architecture relies heavily on dependency injection, meaning each layer receives the objects it can call. Notice how all dependencies point inward. Which means outside layers depend on the inside layers. Which means a file in an inner circle never import a file from an upper level. If it does, something probably failed.

❌ One might be tempted to inject dependencies through function arguments or by using a higher order function like this:
```
// Example for a service accepting dependencies through arguments
func CreateUser (username, email, password, userDatabase) {
  // Validate data
  // Save user to database
  // Send user an email
}

or

// Example of a handler accepting dependencies through a higher order function
func CreateUserHandler (userService) func (w http.ResponseWriter, r *http.Request) {
  return func (w http.ResponseWriter, r *http.Request) {
    // Handle http request
    // Call the service
    // Send http response
  }

}

```

✔️ It would work. But instead, we will use a very idiomatic-Go approach:
```
// Example for a service
type UserService struct {
   // Put there what the service needs to call
   userDatabase
}

// Then define actions for this service
func (s *UserService) CreateUser (username, email, password) {
  // Validate data
  // Save user to database
  // Send user an email
}
```

## Zoom on the control flow

Let's take a last look on 4 different diagrams that I think helps clarify the structure:

![fig3](./README/hexagonal_slice.png "fig3")

// todo: rewrite diagrams

### Slice 1: Zoom on ports and adapters

// todo

### Slice 2: Zoom on dependencies

// todo

### Slice 3: Zoom on the control flow

// todo

### Slice 4: Where to find parts in the folders

// todo

# The folder structure 📁

The structure of folders should reflect the separation between each layer. What I propose here is of course the result of many hours of thinking and refactoring, but it is still not definitive and could benefit some external opinion.

It starts with a classic cmd / src / pkg tree.

```
root
├── cmd -------------------> contains entry points to the program
|   └── httpserver --------> calls httpserver.Start()
├── src -------------------> private application code (contains all layers)
|   ├── core --------------> contains all the business logic (models, services, ports).
|   |   ├── domain
|   |   |   ├── models
|   |   |   └── types
|   |   ├── ports
|   |   |   ├── primaryports
|   |   |   └── secondaryports
|   |   └── services
|   ├── infrastructure ----> contains all secondary actors, pproviders, the router and the registry.
|   |   ├── db
|   |   ├── httpserver ----> contains the routes, the Start() function
|   |   └── registry ------> links all ports and adapters together on start and provide controllers to the high level app 
|   └── interface ---------> contains the interface layer (repository, handlers, middlewares).
|   |   ├── primaryadapters -------> handlers / middlewares
|   |   └── secondaryadapters -----> repository implementation
├── pkg -------------------> shared code, library-wrappers...
├── tests -----------------> high level tests / mock data / mocked infrastructure
├── .env ------------------> secrets
└── Makefile
```

Note you will find advanced informations on how to structure your project in the links bellow.

# Managing packages 📦

I will stress here that you should use Go modules if your version of Go allows it. 

Common practises include:
- Forking packages you need and use those as dependencies.
- Host packages you need as static files on a server, and copy it during the CI/CD process.
- Some project actually commit the vendor folder. If you rely on a very few packages it might be a good idea but just so you know, NodeJS developpers are not going to like it :D 

# How to know which dependency to inject ?

A real life application uses a lot of packages in the business layer. Probably at least uuid generator, an orm, bcrypt...  The first question I had when starting to implement this pattern was wether or not all dependencies must absolutely be injected. 

They technically should.

But in a real life situation and a small project, I'm allowing it. This is the set of rules I follow (and yes there is room for discussion arround them I guess):
- Any package from standard library that doesn't mess with tests can be imported directly without dependency injection. It means it's ok to import anything that performs crypto operations, string manipulation etc... It means it is not ok to import logs or http, because they perform unwanted opertions for tests.
- Any package from 3rd party provider is wrapped, put into /pkg folder and could be imported directly following the same set of rules. It means it would be ok to import directly a uuid generator, bcrypt, a type validation library... They do not mess with our tests. It means it is not ok to import directly a database, a logging library etc...

# Make a Makefile

// todo



=================



## Create an entity

## Create an entity port

## Create a service

## Create a service port

## Create a controller

## Use a registry to  connect ports and adapters together 

## Use a router for http requests

## Create a middleware

## Connect a database

## Connect a new secondary actor

## Unitests 🧪

## How to create a new service

## How to create a new use case ? endpoint + handler

## Pub/Sub Workers

## Queues and workers

## Chron jobs ⏰

## Secrets 🤐

## Docker 🐋

## External ressources

### About Go & Standard practises

- Go best practises by Uber https://github.com/uber-go/guide 
- Go standards on how to structure a project: https://github.com/golang-standards/project-layout
- The Twelve-Factor App: https://12factor.net/
- Semantic versioning: https://semver.org/

### About Hexagonal Architecture

- The original article about hexaagonal architectur from Alistair Cockburn: https://alistair.cockburn.us/hexagonal-architecture/
- A clean implementation in Go by Matías Varela: https://medium.com/@matiasvarela/hexagonal-architecture-in-go-cfd4e436faa3
