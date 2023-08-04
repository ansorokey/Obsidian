## Promises
What is a promise and why do we use them? A promise is a simplification to chaining callback functions in asynchronous code. Recall that asynchronous code must be chained inside one another, since async code runs AFTER syncronous code.

```js
function sayFirst(){
	console.log('First');
}

function saySecond(){
	console.log('Second');
}

setTimeout(sayfirst, 0);
saySecond();

//will print:
//Second
//First
```

In the example above, despite having a 0 millisecond delay, the setTimeout function runs after the code that comes after it. In order to have something else run asyncronously, we would have to chain it. One or two is okay, but chaining several long functiuons ruins the clarity of the code.

```js
setTimeout(() => {

  console.log('First');

  setTimeout(() => {

    console.log('Second');

    setTimeout(() => {

    console.log('Third');

    }, 1000);

  }, 1000);

}, 1000);

console.log('Synchronous runs first');

//Synchronous runs first
// 1 Second delay
//First
//1 second delay
//Second
//1 second delay
//Third
```

All the above does is print messages one second after the other, but is increasingly difficult to follow the more we add on. A *promise* allows us to perform a series of asynchronous actions written synchronously. A promise itself is an object that will eventually be given a value when the asynchronous code completes. Since it is an object with an *eventual* value, a promise can be in one of three states:
- <mark style="background: #FFB8EBA6;">pending</mark> - has not yet been given a value
- <mark style="background: #FFB8EBA6;">fulfilled</mark> - given the successful value
- <mark style="background: #FFB8EBA6;">rejected</mark> - given the failed value
*Whether the promise is fulfilled or rejected, the promise then becomes settled. A failed value is still a value.*

### Creating a promise
Since a promise is an object, a promise is created using a constructor. Within the promise constructor, the *executor* - which is a callback function - will be passed in. This callback function can have two arguments - *resolve* and *reject*. These will act as callback functions that return a successful or failed value to the promise. Only one of these callback functions can be invoked, and only one can be returned at any given time.

*immediate fulfillment*
```js
const bool = true;

const prom = new Promise((resolve, reject) => {
	if(bool){
		resolve("promise was successful");
	} else {
		reject("promise failed");
	}
});

console.log(prom);
// Promise {"promise was succesful"}
```

*delayed fulfillment*
```js
const bool = true;

const prom = new Promise((resolve, reject) => {
	if(bool){
		setTimeout(() => resolve("promise was successful"), 1000);
	} else {
		reject("promise failed");
	}
});

console.log(prom);
// Promise { <pending> }
```
*the value of a promise is whatever is inside of the resolve or reject callbacks when they are invoked. This can be of any data type. When a promise returns a rejected value, it is usually thrown as an error, and cannot be logged normally. A promise will only have a value when a value is given through resolve or reject*

The values from the resolve and reject arguments become the value of the promise object. When we chain asynchronous functions that return promises, the value of the promise is what is passed on.

### Chaining Async Functions
Now that we have a promise which will eventually be given a value, we can pass that value on by chaining `.then` statements. A `.then` statement can be chained to anything that evaluates to a promise: meaning either the promise or a promise returning function. A `.then` statement on its own also returns a promise as a value, so several chained together will continue to return and pass on promises to one another.

```js
const returnPromise = () => {
	return new Promise((resolve, reject) => {
		resolve('Resolution');
	});
}

returnPromise()
	.then(p => console.log(p)) //logs original promise value
	.then(() => 'hello')       // returns a promise with string value
    .then(p => console.log(p)); //logs promise value
//Resolution
//hello
```

*Notice how the chained statements are written. This is the same as writing it all out on a single line, but is much more legible this way!*

A then statement can technically take *two* functions as arguments: one for a resolved promise and one for a rejected promise. 

```js
promise.then(onResolve, onReject);
```

Although we could pass in two functions to a then statement, it is preferred to use a `.catch` statement instead. A `.catch` statement runs any time a promise in a chain of then statements is either rejected *or* if an error is thrown. As such, a catch statement comes at the end of the chain of `.then` statements. A rejected promise or an error will bypass any then statements and immediately jump to the catch for execution. This is because we did not give the then statements an error function. If they had one, the promise would stop there and run it. A catch statement ONLY has an error handling function

*rejected promise bypassing then*
```js
const returnPromise = () => {
	return new Promise((resolve, reject) => {
		reject('error value');
	});
}

returnPromise()
	.then(p => console.log(p))
    .then(() => 'hello')
    .then(p => console.log(p))
    .catch(p => console.log(p));
    //error value
```

*bypassing by throwing an error*
```js
const returnPromise = () => {
	return new Promise((resolve, reject) => {
		resolve('passing value');
	});
}

returnPromise()
	.then(p => {
		console.log(p);
		throw 'error';
		})
    .then(() => 'hello')
    .then(p => console.log(p))
    .catch(p => console.log(p));
    //passing value
    //error
```

These built-in promise functions essentially act as try/catch blocks.

As a reminder, a promise and the functions related to it occur asynchronously. They are simply written in a way that *appears* synchronous. The next then statement will not run until the previous promise is fulfilled, no matter how long it might take. These steps can all take any amount of time, but will run in order from each other.

### Promise.all();
If we needed several promises fulfilled *(meaning resolved OR rejected)* at the same time, we could use the `Promise.all()` method. `Promise.all()` takes in an array of promises *(or functions that evaluate to promises)* and will return a promise. If all promises resolve, the value of the *super promise* returned will be an array of all resolved values. If a single promise inside is rejected, the super promise will return a promise with the same rejected value.

```js
const resPromise = new Promise(resolve => resolve('Passing'));
Promise.all([resPromise, resPromise, resPromise])
	.then(arr => console.log(arr));
// [ 'Passing', 'Passing', 'Passing' ]

const rejPromise = new Promise((resolve, reject) => reject('Failed'));
const superResolve2 = Promise.all([resPromise, resPromise, rejPromise])
	.then(arr => console.log(arr))
    .catch(reason => console.log(reason));
//'Failed'
```

The resolved promises passed into `Promise.all()` are returned in the order they are given. One promise may take longer than others to fulfill, but they will remain in the same order. *the value returned by Promise.all() is still a promise, but with an array of values as a resolved value*
`Promise {[val, val, val]}`

```js
const oneSec = new Promise(resolve => setTimeout(() => resolve('One Second'), 1000));
const twoSec = new Promise(resolve => setTimeout(() => resolve('Two Seconds'), 2000));
const instant = new Promise(resolve => resolve('instant'));

Promise.all([oneSec, instant, twoSec,])
	.then(arr => console.log(arr));
// [ 'One Second', 'instant', 'Two Seconds' ]
```

## Async/Await
A recent update to javascript made it possible to declare functions as asyncronous and return a promise. Doing so allows us access to the `await` keyword. These acts as simple alternatives to chaining `.then` statements. Code written in async/await functions will still be asyncronous, but run syncronously.

To create the function, simply add the keyword `async` before the function definition

```js
async function doStuff() {
	...
}

const doStuff = async function() {
	...
}

const doStuff = async () => {
	...
}
```

Any function we create as async will always return a promise. Even if we return something different inside the function.

```js
const doStuff = async () => {
	return "hello";
}

//Promise {"Hello"}
```

Now that we have created an async function, we can use the keyword `await` inside it. Await can *ONLY* be used *INSIDE* an async function. It will not work outside any function that is not declared async. 

The await keword tells the code to pause here, and *wait* for an ayncronous function to fulfill its promise. *Remeber that a promise starts as pending, and either resolves or rejects. Either outcome is fulfilled.* Assigning a value to something using await will return the *value* of the promise, instead of the promise itself.

```js
let noAwaitPromise = async function () {
	let p = new Promise((resolve, reject) => {
		setTimeout(() => resolve('No Await'), 3000);
	});
	console.log(p);
}

let awaitPromise = async function () {
	let p = await new Promise((resolve, reject) => {
		setTimeout(() => resolve('Await'), 3000);
	});
	console.log(p);
}

noAwaitPromise(); // logs Promise {<pending>}
awaitPromise(); //logs Await
```

We can also place await keywords directly in front of promises, which will wait for them to resolve. If we add an await keyword in front of the `noAwaitPromise` function, we can log the value instead of the promise.

```js
let noAwaitPromise = async function () {
	let p = new Promise((resolve, reject) => {
		setTimeout(() => resolve('No Await'), 3000);
	});
	console.log(await p);
}
//logs No Await
```

Async/await functions and `.then` statements can be used together. Remeber that an async function returns a promise. So if we want to access the value of an async function, we need to chain a `.then` statement to the end of it, since we cant use await *outside* the function.

```js
let awaitPromise = async function () {
	return await new Promise((resolve, reject) => {
		setTimeout(() => resolve('Await'), 3000);
	});
}

awaitPromise()
	.then(p => console.log(p));
```

### Error Handling Async/Await
Unlike `.then()` and `.catch` statements - which are functions specifically for running fulfilled or rejected promises - async/await defined the entire function. This means there is no longer any built in error handling. Instead, we can catch errors using try/catch blocks.

```js
async function handlePromise() {
  try {
    const res = await getPromise();
    console.log('resolved with', res);
  } catch (err) {
    console.log('rejected because of', err);
  }
}

//on success, logs the resolved value
//on rejection or failure, logs error
```

## AJAX
*AJAX* - Asynchronous Javascript And XML - is a web deisgn principle that allows us to create dynamic web pages. Remeber that when we use html, we would send information to a saerver, and the response would be the entire webpage, which would have to be recreated and reloaded. This means every little click of a button essentially amounts so more reloading and more traffic.

Using AJAX principles, we can instead send informartion to a server and be given a response that modifies only a small portion of the webpage dynamically. This is a lighter load on traffic and saves us from creating too many new pages in exchange for lsightly more complexity on the backend of things.

## Fetch
*Fetch()* is a key component of Ajax design. The fetch API is an asynchronous function that performs an http request to on a browser. *feth is built in to browsers, but not node. We can write fetch functions into node for importing into our webpage, but fetch will not do anything in node. Must use browser for testing.*

The fetch function can take in two arguments: a url/path in the format of a string (required), and an object containing additional information (optional).

The options object is not necessary to perform a fetch request, but allows us to set more details and options for the request. Without an options object, the fetch request will run with some default values, such as a GET http method. Adding the options will allow us to change the method, set the content type, and provide a body to the request.

```js
fetch(url);

fetch(url, {
	  method: method,
	  headers: headers,
	  body: body
});
```

A fetch will return a promise. If fulfilled, the value will be the `response` object sent by the server. This will hold information including the status code, url, and response body. Since a fetch request returns a promise, we cannot return any information outside a fetch request besides the promise. Instead, we can perform whatever actions we need to within `.then` chains, or inside an async function using async/await.

```js
//using async/await
let logURL = async () => {
	let response = await fetch('https://api.thecatapi.com/v1/images/search');
	let responseBody = await response.json();
	console.log(responseBody);
}

logURL();

//using chaining
fetch('https://api.thecatapi.com/v1/images/search')
	.then(p => p.json())
	.then(p => console.log(p));
```

### Options
When we do set options in the fetch, thwey will be placed within a single object, and they will be given key-value pairs.
- The method will be in quotes, and be onme of the 5 methods specified
- The headers will be an object containing a json format key-value pairt for each header
- The body will be one of a few formats, depending on the content type established earlier

```js
fetch(url, {
	method: "POST",
	headers: {
		"Content-Type": "application/x-www-form-urlencoded"
	},
	body: "comment=works+well&starRating=4"
});

fetch(url, {
	method: "POST",
	headers: {
		"Content-Type": "application/json"
	},
	body: JSON.stringify(data);
});
```
*We can use the `JSON.stringify()` method to turn passed in data like objects into json format*

## Web API
An API - Application Programming Interface - is in essentially a collection of software. What makes an API special is that it will only perform the things we want it to. A *web API* is a collection of software that only gives out the information we specift it to give. This prevents clients from accessing anything unintended, granting greater responsiveness and functionality to a web server.

## JSON review
<mark style="background: #BBFABBA6;">REMEBER!</mark> JSON is simply a string. It is formatted in a very specific way, but is still simply a specially formatted string.

When formatting javascript objects, the keys will be formatted as strings rather than literals. All objects that are not numbers or booleans will need to changed into strings. The contents of data structures like arrays and other objects will also need to be slightly formatted in a similar way.

```js
{
	"name": "value",
	"key": value,
	"object": {
		"key": "value"
	},
}
```

We have access to two useful json functions. One will format data into a string, the other will turn a string out of json.

```js
//to a single json string
JSON.stringify(data);

//from json
JSON.parse(data);
```

## API RESTful Documentation
Since APIs are common and widely, easily available for use, it makes sense that there is a standard way to access them. These are RESTful conventions, and are common among most APIs. API's can utilize all CRUD mehods, and reference the a resource found at a path.

CRUD Methods
- GET
- POST
- PUT
- PATCH
- DELETE

RESTful Endpoints
- /resource-name
- /resource-name/:resourceId
- /resource-name/:resourceId/nested-collection
- /resource-name/:resourceId/nested-collection/:nested-resourceId

There is a standard convention for how CRUD methods and RESTful endpoints are used together in an API server, which is different from a traditional HTML server since we are only supplying the data requested, and not an entire page.

```
GET /resource
POST /resource
GET /resource/:item-Id
PUT /resurce/:item-Id
```