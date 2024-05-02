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

## Sagas and async calls

````
import { put, takeEvery } from 'redux-saga/effects'

const delay = (ms) => new Promise(res => setTimeout(res, ms))

// ...

// Our worker Saga: will perform the async increment task
export function* incrementAsync() {
  yield delay(1000)
  yield put({ type: 'INCREMENT' })
}

// Our watcher Saga: spawn a new incrementAsync task on each INCREMENT_ASYNC
export function* watchIncrementAsync() {
  yield takeEvery('INCREMENT_ASYNC', incrementAsync)
}
````
Sagas are implemented as Generator functions that yield objects to the redux-saga middleware. The yielded objects are a kind of instruction to be interpreted by the middleware.** When a Promise is yielded to the middleware, the middleware will suspend the Saga until the Promise completes.** In the above example, the incrementAsync Saga is suspended until the Promise returned by delay resolves, which will happen after 1 second.

Once the Promise is resolved, the middleware will resume the Saga, executing code until the next ``yield``. In this example, the next statement is another yielded object: the result of calling ``put({type: 'INCREMENT'})``, which instructs the middleware to dispatch an INCREMENT action.

``put`` is one example of what we call an Effect. Effects are plain JavaScript objects which contain instructions to be fulfilled by the middleware. When a middleware retrieves an Effect yielded by a Saga, the Saga is paused until the Effect is fulfilled.

So to summarize, the ``incrementAsync`` Saga sleeps for 1 second via the call to delay(1000), then dispatches an INCREMENT action.

Next, we created another Saga ``watchIncrementAsync``. We use ``takeEvery``, a helper function provided by redux-saga, to listen for dispatched INCREMENT_ASYNC actions and run incrementAsync each time.

Now we have 2 Sagas, and we need to start them both at once. To do that, we'll add a rootSaga that is responsible for starting our other Sagas. In the same file sagas.js, refactor the file as follows:
````
import { put, takeEvery, all } from 'redux-saga/effects'

export const delay = (ms) => new Promise(res => setTimeout(res, ms))

export function* helloSaga() {
  console.log('Hello Sagas!')
}

export function* incrementAsync() {
  yield delay(1000)
  yield put({ type: 'INCREMENT' })
}

export function* watchIncrementAsync() {
  yield takeEvery('INCREMENT_ASYNC', incrementAsync)
}

// notice how we now only export the rootSaga
// single entry point to start all Sagas at once
export default function* rootSaga() {
  yield all([
    helloSaga(),
    watchIncrementAsync()
  ])
}
````

This Saga yields an array with the results of calling our two sagas, helloSaga and watchIncrementAsync. This means the two resulting Generators will be started in parallel. Now we only have to invoke sagaMiddleware.run on the root Saga in main.js.
````
// ...
import rootSaga from './sagas'

const sagaMiddleware = createSagaMiddleware()
const store = ...
sagaMiddleware.run(rootSaga)
````

## Declarative effects
In ``redux-saga``, Sagas are implemented using Generator functions. To express the Saga logic, we yield plain JavaScript Objects from the Generator. We call those Objects Effects. An Effect is an object that contains some information to be interpreted by the middleware. You can view Effects like instructions to the middleware to perform some operation (e.g., invoke some asynchronous function, dispatch an action to the store, etc.). To create Effects, you use the functions provided by the library in the ``redux-saga/effects`` package.




