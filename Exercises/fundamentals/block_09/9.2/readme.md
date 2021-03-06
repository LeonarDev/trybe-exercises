# (9.2) Promises and APIs

- [Promises](#Promises)
- [Creating a Promise](#Creating-a-promise)
- [Using Promises](#Using-Promises)
- [Chaining Promises](#Chaining-Promises)
- [Async and Await](#Async-and-Await)
- [Application Programming Interface](#Application-Programming-Interface)
- [Exercises](https://github.com/LeonarDev/Trybe/edit/main/Exercises/fundamentals/block_09/9.2/exercises)

<br>

<img src="https://media1.tenor.com/images/e2fdba6a5bcdc347b80279ff8f28c133/tenor.gif">

### Promises

> I promise to do this whenever that is true. If it isn't true, then I won't.

Promises are the main way to communicate with APIs.

**Complementing the explanation:**

A promise is used to handle the **asynchronous result** of an operation. 

JavaScript is designed to not wait for an asynchrnous block of code to completely execute before other synchronous parts of the code can run. For instance, when making API requests to servers, we have no idea if these servers are offline or online, or how long it takes to process the server request.

With Promises, we can defer execution of a code block until an async request is completed. This way, other operations can keep running without interruption.

Promises have three states:
- **Pending**: This is the initial state of the Promise before an operation begins
- **Fulfilled**: This means the specified operation was completed
- **Rejected**: The operation did not complete; an error value is usually thrown
<hr>

<br>


## Creating a Promise

The Promise object is created using the new keyword and contains the `promise`; this is an executor function which has a **resolve** and a **reject** callback. 

In the same way that a "normal function" has the `return` to return a response from its execution, Promise has the `resolve` and the `reject`, which do exactly that: they return a response from its execution.

`Resolve` returns a **positive response**, that is, you will use it when you want to convey that everything went well.

The ``reject` returns a **negative response**, that is, you will use it when something goes wrong.

There is only a small difference between the `resolve/reject` and the `return`: 
  - While the `return` **stops** executing the function when it is called, 
  - The `resolve/reject` **does not stop**, so it is important to use a return before one when you don't want it the execution continues (this pattern is called **'early-return'**).

```js
const promise = new Promise(function(resolve, reject) {
  // promise description
})
```

Let’s create a promise:

```js
const weather = true
const date    = new Promise(function(resolve, reject) {
  if (weather) {
    const dateDetails = {
      name:     'Cubana Restaurant',
      location: '55th Street',
      table:    5
    };

    resolve(dateDetails)
  } else {
    reject(new Error('Bad weather, so no Date'))
  }
});
```

If `weather` is `true`, resolve the promise returning the data `dateDetails`, else return an error object with data `Bad weather, so no Date`.
<hr>

<br>


## Using Promises

Using a promise that has been created is relatively straightforward; we chain `.then()` and `.catch()` to our **Promise** like so:

```js
date
  .then(function(done) {
    // the content from the resolve() is here
  })
  .catch(function(error) {
    // the info from the reject() is here
  });
```

Using the promise we created above, let's take this a step further:

```js
const myDate = function() {
  date
    .then(function(done) {
      console.log('We are going on a date!')
      console.log(done)
    })
    .catch(function(error) {
        console.log(error.message)
    })
}

myDate();
```

Since the weather value is `true`, we call `mydate()` and our console logs read:

```js
We are going on a date!
{
  name: 'Cubana Restaurant',
  location: '55th Street'
  table: 5
}
```

`.then()` receives a function with an argument which is the **resolve** value of our promise. `.catch` returns the **reject** value of our promise.

>Note: **Promises are asynchronous**. Promises in functions are placed in a micro-task queue and run when other synchronous operations complete.
<hr>

<br>


## Chaining Promises

Sometimes we may *need to execute two or more asynchronous operations* based on the result of preceding promises. In this case, promises are chained. Still using our created promise, let’s order an uber if we are going on a date.

So we create another promise:

```js
const orderUber = function(dateDetails) {
  return new Promise(function(resolve, reject) {
    const message = `Get me an Uber ASAP to ${dateDetails.location}, we are going on a date!`;

    resolve(message)
  });
}
```

This promise can be shortened to:

```js
const orderUber = dateDetails => {
  const message = `Get me an Uber ASAP to ${dateDetails.location}, we are going on a date!`;
  return Promise.resolve(message)
} 
```

We chain this promise to our earlier date operation like so:

```js
const myDate = function() {
  date
    .then(orderUber)
    .then(function(done) {
      console.log(done);
    })
    .catch(function(error) {
      console.log(error.message)
    })
}

myDate();
```

Since our weather is true, the output to our console is:

```js
Get me an Uber ASAP to 55th Street, we are going on a date!
```

Once the `orderUber` promise is chained with `.then`, subsequent `.then` utilizes data from the previous one.
<hr>

<br>


## Async and Await

### Async

An **async function** is a modification to the syntax used in writing promises. You can call it syntactic sugar over promises. It only makes writing promises easier.

An *async function returns a promise** -- if the function returns a value, the promise will be resolved with the value, but if the async function throws an error, the promise is rejected with that value. 

Let’s see an async function:

```js
async function myRide() {
  return '2017 Dodge Charger';
}
```

and a different function that does the same thing but in promise format:

```js
function yourRide() {
  return Promise.resolve('2017 Dodge Charger');
}
```

From the above statements, `myRide()` and `yourRide()` are equal and will both resolve to `2017 Dodge Charger`. Also when a promise is rejected, an async function is represented like this:

```js
function foo() {
  return Promise.reject(25)
}

// is equal to:

async function() {
  throw 25;
}
```

<br>

### Await

The await keyword is used in an async function to ensure that all promises returned in the async function are synchronized, ie. they wait for each other. 

Await eliminates the use of callbacks in `.then()` and `.catch()`. In using async and await, async is prepended when returning a promise. `try` and `catch` are also used to get the rejection value of an async function. Let's see this with our date example:

```js
async function myDate() {
  try {

    let dateDetails = await date;
    let message     = await orderUber(dateDetails);
    console.log(message);

  } catch(error) {
    console.log(error.message);
  }
}
```

Lastly we call our async function:

```js
(async () => { 
  await myDate();
})();
```
<hr>

<br>


## Application Programming Interface

An **API** allows applications to communicate with each other, serving as a "bridge" for them. An API is not a database or a server, but the one responsible for controlling access points to them, through a set of routines and programming standards

<img src="https://course.betrybe.com//fundamentals/js-asynchronous/API.png">

<br>

> Every API is a backend, but not every backend is an API.

A standard website backend returns templates for the front end of a single application, through defined routes. For example, when you access a page on our platform, you are making a **request** to the server, which returns a template as a **response**.

The APIs also have access routes that allow communication with the server, but they do not need to return templates. Generally, they return data in **JSON** format.
