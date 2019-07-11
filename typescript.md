---
title: "Typescript: A Basic Overview"
theme: moon
---

## Typescript 101
### A CPA Global Presentation

---
# Overview 
* What is Typescript
* Why is it important
* Basic features
* Benefits over ECMAScript
* Getting Started


---

# What is Typescript?
<img src="https://raw.githubusercontent.com/remojansen/logo.ts/master/ts.png" alt="Typescript Logo" style="width:200px">

----

> "Typescript is a superset of Javascript that compiles down to plain Javascript"

* Write Typescript
<!-- .element: class="fragment" data-fragment-index="1" -->
* Compile
<!-- .element: class="fragment" data-fragment-index="2" -->
* Run compiled code
<!-- .element: class="fragment" data-fragment-index="3" -->

---

# Why is Typescript important?

----

## Typescript is widely used 
### ... and growing fast
<!-- .element: class="fragment" data-fragment-index="1" -->
* 61% of of Javascript developers use Typescript
<!-- .element: class="fragment" data-fragment-index="2" -->  
* 31% in 2018
<!-- .element: class="fragment" data-fragment-index="2" -->

----

## Typescript is supported by all major frameworks
<div style="text-align: left">
  * Angular *
  * Vue *
  * React
  * Node
  * Nest
</div>
'*' = Default Option

----

## Typescript is the ONLY option for Pure Angular
#### Modernized forecast-ui work will require Typescript knowledge

---

# Basic Features

----

## Strict Typing

```typescript
const hello: string = "Hello World!" 
const thisIsANumber : number = 5
const iDontWantToDeal : any = "The 'any' type allows you to work just like ECMAScript"
const iHaveNoClue: unknown = "The unknown type is similar to any, but with type checking when"
```

----

## Object Oriented Design
```typescript
export interface Pet {}
export class Dog implements Pet {}
export Enum Breeds = {}
export function genericFunction<T>(arg: T) : T {}
```
----
## Annotations
```typescript
@JsonController()
export class MovieControler {
    private repository = getRepository(MovieEntity);

    @Get("/movies")
    getAll() {
        return this.repository.find()
    }

    @Post("/movie")
    add(@Body() movie: MovieEntity) {
        return this.repository.insert(movie);
    }

    @Put("/movie")
    edit(@Body() movie: MovieEntity) {
        return this.repository.save(movie);
    }
```

----

## Compatibility
* Can target latest tc39 standards
<!-- .element: class="fragment" data-fragment-index="1" -->
* Compiles down to ES3 by default
<!-- .element: class="fragment" data-fragment-index="2" -->
---
# Benefits over ECMAScript

----

## Higher Code Quality
* Typing allows catching of assignment errors
* No more bad variable names or method calls
* 38% of AirBnb bugs could be prevented by Typescript
<!-- .element: class="fragment" data-fragment-index="1" -->
----

## Easier Development
### IDE Autocompletion

---

# Getting Started

--- -
* http://www.typescriptlang.org/play/
* https://angular.io/start
* https://github.com/TypeStrong/ts-node
* https://medium.com/@karl.lee/vue-cli-3-typescript-a-quick-start-guide-324a18859485

---

# Questions?