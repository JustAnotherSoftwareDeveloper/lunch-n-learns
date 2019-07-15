---
title: "Angular: A Basic Overview"
theme: moon
---

## Angular 101
### A CPA Global Presentation

---
# Overview
----
* What is Angular
* Differences with AngularJS
* Technologies Used
* Angular CLI
----
* Data Binding
* Components
* Modules
* Services
* Directives
* Getting Started

---
## What is Angular
<img src="./assets/angular.svg" alt="Angular Logo" style="width:400px"/>
----
>Angular is a TypeScript-based open-source web application framework led by the Angular Team at Google and by a community of individuals and corporations. 
>Angular is a **complete rewrite** from the same team that built AngularJS
---
## Differences with AngularJS
----
### No more $scope
-----
* State is handled at a component level
* State must be passed through components
* `this` property in classes similar to $scope
----
### Controllers -> Components
-----
* Modular view with html template and Typescript logic
* similar in syntax to Angular directives
----
### Improved two-way data binding
-----
* `[]` is reading from Typescript
* `()` to handle events (eg. `(click)` )
* `[()]` for two way data binding 
----
### Modular Styling
-----
* Styling is per-component
* styles.css is global
* ::ng-deep ensures descendents get styling
----
### Performance Improvements
-----
* Component level code splitting (Ivy)
* Code Splitting (smaller bundles)
* Faster (due to improved binding)
---
## Technologies Used
----
* RxJs
* Typescript
* Redux (if using @ngrx)
* scss/sass
* Jest
---
## Angular CLI
----
### Angular CLI is a command line tool that handles most common Angular functions
`npm i -g @angular/cli`

----
### `ng generate`
* Creates base of angular files
* eg. `ng generate component my-component` creates a folder called "my-component" containing relevant files
----
### `ng add`
* uses `npm install`
* can run special, angular specific, scripts automatically if included in library

----
### `ng update` 
* Updates packages to latest version
* Runs any included update scripts
* Does not guarantee compatibility
----
### `ng serve`
* Serves web application
* watch 
* live-reload
----
### `ng test` / `ng lint`
* Improvements on VanillaJS equivalents
* Reads from `angular.json` for configuration
----
### `ng build`
* Builds application
* Uses Webpack on backend
* Reads from `angular.json` for configuration
---
## Data Binding
----
### Angular uses two way data binding
* @ngrx can effectively change this to MVVC'
* can specify one way data binding
----
### Inputs
```typescript 
@Input() passedInVar: string = "default value"
```
* Used when passing data into components
* `[]` annotation can be used on native HTML element properties
* `<div [id]="passedInVar"><div>`
----
### Outputs
* Used when events are emitted 
* `()` annotation is used
* `<button (click)="myFunc()"></button>`
----
### Form Control
* More advanced version of ngModel
* More control over validation and model changes
* Much more verbose
---
## Components
----
### Core of Angular's HTML templates
```html
<body>
    <my-app></my-app>
</body>
```
----
### Components are modular
```html
<div class="container-12 h-100">
    <div class="row h-100">
        <div class="col-4"> 
            <my-sidebar></my-sidebar>
        </div>
        <div class="col-8">
            <main-view></main-view>
        </div>
    </div>
</div>
```
### Can construct reusable components
```html
<my-datepicker id="input1" [(ngModel)]="var1"></my-datepicker>
<my-datepicker id="input2" [(ngModel)]="var2"></my-datepicker>
```
---
## Modules

* Collection of components, directives, pipes, and services to be exported
* Can then be imported into other libraries
* Facilitates Angular Ivy component-level splitting
* Imports modules for dependency injection
---
## Services
* Files that contain code not tied to html
* Most common: Services for HTTP requests
* @ngrx heavily utilizes services
* Facilitates Testing
---
## Directives
----
### Properties that go on elements
* alter the nature of the element attached
* elements cannot have more than one directive4
----
### Most used on inputs
```html
    <div>
        <div *ngFor="let date in allDates">
            <input customDatepicker [(ngModel)]="date">
        </div>
    </div>
```
---
## Getting Started
* https://angular.io/start
* https://ngrx.io/
---
# Quesitons