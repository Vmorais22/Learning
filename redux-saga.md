# Redux-Saga

## Introduction

**Middleware**: A middleware is a software that acts as abridge between different components or systems by intercepting its requests or venets before reaching its final destination. In the context of web development, middlewares are between the client and the server and performs actions such as authorization, log in and modification of HTTP requests and responses. They can be pipelined as a set of chained middlewares. In the context of redux, middleware are usually between the action dispatch and the reducer to perform async calls to an API and handle the request / response process, for example, or to perform other async tasks.

``redux-saga`` is a library that aims to make application side effects (i.e. asynchronous things like data fetching and impure things like accessing the browser cache) easier to manage, more efficient to execute, easy to test, and better at handling failures.

The mental model is that a saga is like a separate thread in your application that's solely responsible for side effects. redux-saga is a redux middleware, which means this thread can be started, paused and cancelled from the main application with normal redux actions, it has access to the full redux application state and it can dispatch redux actions as well.

It uses an ES6 feature called Generators to make those asynchronous flows easy to read, write and test. With ES6 generators, we have a different kind of function, which may be paused in the middle, one or many times, and resumed later, allowing other code to run during these paused periods. First, the new declaration syntax:

````
function *foo() {
    // ..
}
````

Notice the * there? That's new and a bit strange looking. To those from some other languages, it may look an awful lot like a function return-value pointer. But don't get confused! This is just a way to signal the special generator function type. 
``yield`` is another special statement. A yield expression pauses the function from inside.
