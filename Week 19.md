## Quick Review on Servers
A client is the device or software that requests information *like a web browser*
A server is the device or software that stores and sends information *like a database*
An application is simply a program. There are client-side applications, and server-side applications. Each serve their own purpose.

RESTful API routes are a standardized convention for how a request/response to a server is structured.
It is usually made up of 3 core parts:
- Path - the address the request is being made to
- Method - what we are attemtping to do with data
- Body - the data supplied or recieved

RESTful API methods are abbreviated as **CRUD** Create (*post*) Read (*get*) Update (*put/patch*) and Delete (*delete*)
These methods are applied to the location of a resource in a server, shich can further be referenced by a supplied resourceID
```
GET /collection
//returns a resource

POST /collection
//adds a new item to a collection

PUT /collection/:collectionID
//updates a specific item

DELETE /collection/:collectionID
//removes an item from a collection
```

Postman is a desktop application that we can use to view customs requests and responses between clients and servers.
To make a request, we select a CRUD method and supply a URL.
![[postman-get.png]]

If we want to include data with a request, we can fill out the body, select the data type (raw and then JSON)
![[postman-post.png]]

## What is Express?
Express is a *Node.js backend framwork*. These are a bunch of fancy words that simply mean a Node.js tool we can use to shortcut long, repetitive code. Just like bootstrap and jQuery are frameworks, so too is Express. It's a library that makes standard code shorter and easier to work with/read, so we can focus on functionality without bloating our code.

### Installing Express
Since express is an optional framework, that means it must be installed in the projects we want to use it in. 

Navigate to the root directory of your project's folder. Initialize  json.package with the following command:
`npm init -y`

And then install express itself using 
`npm install express`

This will create the `package.json` and `package-lock.json` files in the root project directory. *the package.json file is a json text file that keeps track of dependancies needed for the project - the required external code* Express will be one of the dependancies listed

When we run `npm install`, the specified package gets downloaded and installed into the `node_modules` folder, which will contain every single dependancy we bring in. This folder can grow large very quickly. Since we work with git to track and manage changes, we do not want to push and pull hundreds of unnecessary files. Adding a `.gitignore` file to the root project directory, and adding `node_modules/` will prevent the folder from being seen by git and git only.

Once we have Express installed, using it is as simple as importing it an then creating an express app object.
```js
const express = require('express');
const app = express();
```

Now we have access to server functions off of the `app` object
```js
//app.listen(portNumber, cb);
const port = 5000;
app.listen(port, () => console.log('Port is listening on port ' + port));

//app.get(path, cb)
app.get('/', (req, res) => {
	res.send('Hello World');
})
```

### Nodemon
When we create a server and make a request to it, that server is running in the state it was created in. This means that any slight changes or fixed bugs/typos do not get updated live. We would first have to close the server and open it again. This can get tiresome quickly if we are making many changes.

*Nodemon* is a simple dependancy that restarts our server for us, just like *Liveserver* does for html. To install it, allwe have to do is add it to our `package.json` files under dependancies.
```js
"dependancies": {
	"nodemon": "^2.0.13"
}
```
The carrot in front of the version type means that npm can use version 2.0.13 *or higher*

To do the same from the terminal, we can run the following command in root project directory
`npm install -D nodeman`
The `-D` flag is used to install it as a *Dev Dependancy*. Dev Dependancy's are local dependancies only, and do not affect the final product.
To run it, we simply run nodemon with our server file
`nodemon server.js`

Also inside of our `package.json`, we can edit the things such as dscriptions and scripts, which run a command when we type in that specifc command like `npm start`.

### Express Route Handling
Express makes handling incoming requests much simpler. Remember that a request is made to a *URI*, which is the path of the resource we are looking for. '/' is the default root resource, and collections can come after that. The specific resource we request is the *endpoint*. 

Express has a method to handle the route for each CRUD method.
```terminal
npm init -y
npm install express
npm install -D nodemon
```

```js
const express = require('express');
const app = express();

app.get(path, (req, res) => {});
app.post('/', (req, res) => {});
app.put([], (req, res) => {});
app.patch(path, (req, res) => {});
app.delete(path, (req, res) => {});

app.listen(port, cbListen);
```
*Note that the `.listen` method comes last*
The paths that get passed into these route handlers will always be relative to the root resource - starting with '/'. The path can be very flexible, being a string, a regular expression, or an array containing both. Passing in an array will send the same response to any path in the collection. 

The second argument for these methods will be a callback function with `req` and `res` objects as arguments. This is how we will read the request and send a response.
A response can be sent using two methods, depending on the kind of content we want to send. For plain text, we can use `res.send()`, or we can send json using `res.json()`. *Both of these methods will write to the response body AND send it because `res.end()` is inside the code of both methods. *

```js
const data = {
	users: [];
}

app.post('/users', (req, res) => {
	let newUser = {
		name: null,
		age: null
	}
	data.users.push(newUser);
	res.send(newUser);
});

app.get('/users', (req, res) => {
	res.send(data.users);
})
```

### Express vs HTTP Servers
Express is just a shorthand framework library for http servers. Under the hood, they *are* http servers, but provide a much simpler user interface for developers to read and write.

#### Initializing the server

HTTP
```js
const http = require('http');
const server = http.createSever((req, res) => {
	...
});
const port = 5000;
server.listen(port, () => console.log('Listening on port ' + port));
```

Express
```js
const express = require('express');
const app = express();
const port = 5000;
app.listen(port, () => console.log('Listening on port ' + port));
```

#### Creating routes

HTTP
```js
//inside of the createSever callback method
req.on('end', () => {
	if(req.method === 'GET' && req.url.startsWith('/users/')){
	...}
});
```

Express
```js
//in the global scope
app.get('users/:userId', (req, res) => {
	...
});
```

#### Parsing the request body

HTTP
```js
// Within createServer
let reqBody = "";
req.on("data", (data) => {
    reqBody += data;
});

req.on("end", () => {
    if (reqBody) {
        req.body = reqBody
        .split("&")
        .map((keyValuePair) => keyValuePair.split("="))
        .map(([key, value]) => [key, value.replace(/\+/g, " ")])
        .map(([key, value]) => [key, decodeURIComponent(value)])
        .reduce((acc, [key, value]) => {
            acc[key] = value;
            return acc;
        }, {});
    }
}
```

Express
```js
//in the global scope
app.use(express.json());
```

#### Parsing URL Parameters

HTTP
```js
//in the createServer method callback function
req.on('end', () => {
	if (req.method === 'GET' && req.url.startsWith('/users/')) {
		const urlParts = req.url.split('/');
		if (urlParts.length === 3) {
			const userId = urlParts[2];
			...
		}
	}
});
```

Express
```js
//in the global scope
app.get('users/:userId', (req, res) => {
	const userId = req.params.userId;
	...
});
```
*In express, url parameters are automatically stored for us in the req.params object, and can be referenced by name/notation. Keep in mind however that ANYTHING in the spot of an ID will be stored and read as a parameter*

#### Sending a response

HTTP
```js
//in the createSever callback function
req.on("end", () => {
    if (req.method === 'GET' && req.url.startsWith('/users/')) {
        const urlParts = req.url.split('/');
        if (urlParts.length === 3) {
            const userId = urlParts[2];
            res.statusCode = 200;
            res.setHeader('Content-Type', 'text/plain');
            res.write('User details for userId: ');
            res.write(userId);
            return res.end();
        }
    }
});
```

Express
```js
//in the global scope
app.get('/users/:userId', (req, res) => {
	const userId = req.params.userId;
	res.status(200);
	res.send(`User details for userId ${userId}`);
});
```
*`res.send()` and `res.json()` both use `res.end` under the hood, so either one will send the response*

#### Final Product

HTTP
```js
const http = require('http');

const server = http.createServer((req, res) => {
  let reqBody = "";
  req.on("data", (data) => {
    reqBody += data;
  });

  req.on("end", () => {
    if (reqBody) {
        req.body = JSON.parse(reqBody);
    }

    // GET /users/:userId
    if (req.method === 'GET' && req.url.startsWith('/users/')) {
      const urlParts = req.url.split('/');
      if (urlParts.length === 3) {
        const userId = urlParts[2];
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/plain');
        res.write('User details for userId: ');
        res.write(userId);
        return res.end();
      }
    }

    // Everything else is not found
    res.statusCode = 404;
    res.setHeader('Content-Type', 'text/plain');
    res.write('Not Found');
    return res.end()
  });

});

const port = 5000;
server.listen(port, () => console.log('Server is listening on port', port));
```

Express
```js
const express = require('express');

const app = express();

app.use(express.json());

// GET /users/:userId
app.get('/users/:userId', (req, res) => {
    const userId = req.params.userId;
    res.status(200).send(`User details for userId: ${userId}`);
})

const port = 5000;
app.listen(port, () => console.log('Server is listening on port', port));
```

### Express Route Order
in express, since everything is in the global scope, we do not need to choose which `app.method()` to run using conditional statements like we do with HTTP. Instead, express will run the FIRST method that matches the request. This makes order matter, since urls are parsed in the CRUD methods automatically. Remeber that express request methods can take in not only strings, but string patterns as well. Meaning a less vague API path will run if it comes before the more specific one.

```js
app.get('/users/*') //applies to a path with ANYTHING that comes after users
//sends response because the path matched first

app.get('/users/:userID') //applies to a specific user
//not reached, response already
```

Since responses can only be sent once per request, we want to make sure the most approriate matching one is sent. Good practice here is to group similar paths together, and put them in the code from most specifc to least specific.

```js
//users path
app.get('/users/:id', (req, res) => {
    res.send();
});

app.get('/users', (req, res) => {
    res.send();
});

//products path
app.get('/products/:id', (req, res) => {
    res.send();
});

app.get('/products', (req, res) => {
    res.send();
});

//purchases path
app.get('/purchases/from/:startDate/to/:endDate/user/:userId', (req, res) => {
    res.send();
});

app.get('/purchases/from/:startDate/to/:endDate', (req, res) => {
    res.send(");
});

app.get('/purchases/user/:userId', (req, res) => {
    res.send();
});

app.get('/purchases/:id', (req, res) => {
    res.send();
});
```

### Request Response Objects
Since express extends the functionality of the http server beneath it, this means that it also uses the `req` and `res` objects for reading requests and sending responses. All methods and properties of the http server are still available to us in Express. But we also have additional properties and methods for both. Here are a few important ones.

```js
req.body // an object containing the parsed request body
req.query // an object containing search queries by keyname and values
req.params // an object containing the request url split into paramatered variables

res.status(num) // sets the status code for the response
res.type({"header": "value"}) // sets response headers in an object
res.send(); //interprets content and send the response
res.json(); //same as above, but specifically for sending json
```
*res.send() can interpret what we are sending and set the correct contenet type header for us. Functionally, it does the exact same thing as res.json, but good practice and readability may be best to choose the correct method and set the correct header*
*Both response sending methods also default to a response code of 200, so be sure to change that to the most approrpiate status code*

### Express Middleware
*Middleware* refers to the code that runs between the start of an express request, and the response sent back. Up until now, we have been passing in only the req and res objects, along with ONE callback function for our express request methods. Express allows us to add any number of callback functions to run in between the start and end of the request method. In addition, we can also pass in up to two more arguments inside those functions - `next`. and `err`.

To make a middleware function, we need to pass in `next` as a parameter to a callback function after req and res, which will get called at the end of the function. This will allow the function to run the *next* piece of middleware - or the next function in line.

```js
const sayHello = (req, res, next) => {
    console.log('Hello!');
    next();
}
app,get('/', sayHello, () => {
	res.send('Hello World');
})
```

Since any number of middleware function can be passed in, we can choose to list several in the arguments, or we can pass in an array of callback functions.
*even the normal, solo anonymous function we pass in is middleware. Remeber, ANYTHING between the request being made and the response is middleware. This means that the response can be routed to different functions, and a response can be sent back from anywhere.*

```js
//list in the method
app.get('/', sayHello, cb2, cb3, cb4, (res, req)) => {
	res.send('Hello World');
};

//pass in array of methods
cbArr = [sayHello, cb2, cb3, cb4];
app,get('/', cbArr, sayHello, () => {
	res.send('Hello World');
});
```
*the only possible arguments or parameters that can be passed into a midleware function are `err, req, res, next`. If we wanted to pass in additional information to the next middleware function, we can remember that `req` and `res` are objects. We can append information to them as properties so they can be accessed in the next function. Be careful not to append this information to a property name that already exists*

If we didnt want to individually add callback functions to every method, we could instead bind a function to the instance of the express server (in this case, `app`) using `app.use(func)`. Every single http method function we run will run the binded function first.
```js
app.use(sayHello);
app.get('/', (req, res) => {
    res.send('Response text');
});
```

The order of an `app.use()` statement determines what will get used and when. Only the `app` methods that come after the `app.use()` will invoke what was passed in. This can be useful for error handling, since we can put the error middleware at the end of the server, and all the code that came before it will *not* make use of the error handlers or unfound routes.

`app.use()` also allows us to pass in a path, that way we can choose when to use middleware funcrtions, so we don't have to run through them all. That piece of middleware will run for any path that begions with the passed in path. Not passing in a path will run on every single path.

## Error Handling in Express
The default error handler in express will throw an error to the console, and then respond with a message to the client - `Cannot METHOD PATH` - and default the status code to 500 (Internal Sever Error).

In order to create a custom error with additional information and different status codes, we could add a custom error handler to our express server. This will run any time an error is thrown, including when a route is not found.

An error handler is simply another function to run in the server, which makes it just another piece of middleware. What makes an error handler unique is the passing in of the fourth possible argument/parameter - `err`. A middleware funcrtion with all four parameters is the definition of an express error handler. This is how they are recognized by the program.

```js
app.use((err, req, res, next) => {
  console.error(err);
  res.send('An error occurred!');
});
```

Remeber that middleware is run between a request and the sending of a response. A response is the end destination. This means that we can throw a custom error in one function and have it sent as a response in a function that takes in all errors. 

```js
const express = require('express');
const app = express();

app.use('/wrongPath1', (req, res, next) => {
    throw new Error('Error: Resource not found');
});

app.use('/wrongPath2', (req, res, next) => {
    throw new Error('Error: Problem with server');
});

//All other route handlers go here

app.use((req, res, next) => {
    throw new Error('Error: No path given');
});

app.use((err, req, res, next) => {
    res.status(400);
    res.send('Error Encoutnered ' + err.message);
});

let port = 3000;
app.listen(port, () => console.log(`listening on port ${port}`));
```

In the example above, we have several functions that exist solely to throw a unique error. These do not need the `res.send()` method, because they will pass their error on to the next function *that has an error parameter*. Anything that cannot take in an error will be ignored. Notice that in these custom error handlers, we can choose what status code to pass in before sending a response. If we wanted the handler to decide whether to use the set status code or a generic default one, we could use *short circuiting*
`res.status(err.status || 500)`
This will work similarly to a ternary statement, where it will assign the *first* truthy value.  If err.status is assigned a value, it will be that. Otherwise, it will be the default value we set instead.

Since throwing an error will instantly end the middleware function that creates it, we can create additional middleware that does something to thet error before passing it on again - such as either logging it to the console or to a database.

```js
app.use((req, res, next) => {
	throw new Error('Resource not found');
});

app.use((err, req, res, next) => {
	if(process.env.NODE_ENV === 'production') {
		//log error to databse
	} else {
		//log error to local console
		console.error(err.message);
	}
	//pass on error to nexr error handler
	next(err);
});

app.use((err, req, res, next) => {
    res.status(err.status || 500);
    res.send('Error Encoutnered ' + err.message);
});
```

## Express Routers
We have seen that we can pass an entire path in to an express method to handle that route. But this can become cumbersome if a single route can lead into many many different methods and endpoints, and clutter our code.

```js
app.get('/home/:a', cb);
app.post('/home/:a', cb);
app.put('/home/:a', cb);
app.patch('/home/:a', cb);
app.delete('/home/:a', cb);

app.get('/home/:b', cb);
//...etc
```

Rather than fill a single file with every single possible path our server can handle, we can decrease the clutter using a *router*. Usually written in another file and imported into the main code, a router is a module that works similarly to a miniature version of an express server, and is initialized like so:

```js
const express = require('express');
const router = express.Router();

router.get('/sub-path', cb);

module.exports = router;
//do not forget to export at the very end of the code to be used by other files
```

*It is good practice to have a directory to store all router files in folder called `routes`, which is where they will be imported from*

```js
const express = require('express');
const router1 = require('./routes/router1');
const app = express();

app.use('/main-path', router1);
```

A router allows us to define a collection of route handlers for a common path beginning. This will call the specified router for all paths that begin with the appropriate path beginning. Since we now have a router looking at how a path opens, we can then make our standard express requests methods a little shorter. *the router will ONLY run if a path is found that begins with the matching path, so we can bind many different routers and not worry about the wrong request going to the wrong place. Inside the router, the request methods will then look for the approriate subpath*

## Serving Static Files
Express allows us to send files hosted locally on the machine. These files should be accessed ina local folder in the project, and should be accessed by a relative path, since the project can run on different machines with different files structures. 

The nehtod used to serve files is `express.static('folder-name')` usually used within `app.use()`.
In order to search for a file, we need to pass in the beginning to a pth, and then tell `express.static` what folder to look into for files.
```js
app.use(express.static('folder-name'))
	//for an empty path, searches a folder and all sub folders

app.use('/stickers', express.static('assets/images'));
	//for a path that begins with 'stickers', searches the folder assets/images for files
```
*if we wanted to account for different project foldres, we can use an absolute path using the following function*
```js
const path = require('path');
app.use('/path-start', express.static(path.join(__dirname, 'folder-name')));
	//we use a path and express.static like ususal
	//when we pass in a pth, we can pass in path.join()
	//this will create a full absolute path for any machine
```

## Environment Variables
An *environment variable* is a variable that can be stored to your development environment. This means that it doesnt need to be set in code - instead it is set in your environment (like node). This can help keep code clear, set default variables for testing or production, or hide sensitive information from the public.

The simplest way to set environament variables is by assigning them on the commands line right before you run your code. Many can be declared at once here. Common practice is to name these in all caps.
*they won't need declaration keywords, just a key and value pair. If the value needs spaces, put the value in quoutes.*
```terminal
PORT=5000 NODE_ENV=development node app.js
```

The next slightly better way to set environment variables is through a script. We can declare these within a package.json line, which will run when we run that script
```json
{
  "scripts": {
    "start": "PORT=8080 NODE_ENV=development node app.js"
  }
}
```

### Retrieving environment variables
All environment variables set this way are stored within the `process.env` object. The `proces` object is a global Node object. This means we can access them anywhere in code by initializing a variable to that value and referencing the key.
```js
const port = process.env.PORT;
const environemnt = process.env.NODE_ENV;
```

### Dotenv

If we have a lot of environmetnt variables to set, instead of typing them out one by one, we can instead create a `.env` file. This is a plaintext file with the environmetn variables listed out in the same format, one per line.
```
// name the file `.env`
PORT=8080
SECRET=password
NODE_ENV=production
```

This file will be placed in the root of the project folder. In order to actually use the file, we need to install the `dotenv` node package.
`npm install dotenv`

Once we install the package, we just need to read the file by importing it and confoguring it in the code.
```js
require('dotenv').config();
```

And now our environment variables are read and set from the file!

Another package we can install is the dotenv-cli package. This does the same thing, but removes the need for us to configure the files in our code. Instead, we can have the command line read and set the `.env` file.

To do this, we will npm install both packages
`npm install dotenv dotenv-cli`

And then add a single command in front when we run our project
`dotenv node server.js`
And that will seek out the `.env` file to load variables

<mark style="background: #ABF7F7A6;">KEEP IN MIND!</mark> Since we upload our projects a remote repository, we don't want private infiormation available to whoever downloads the `.env` file. In order to avoid uploading our private development information, make sure that we include the `.env`  in the `.gitignore`. 

But we still want other people to know what the `.env` file should contain or how to set it up, so instead, we can include example instructions in the README, or upload a seperate `.env` file with the values replaces with standin information

```
PORT=PORT_VALUE_HERE
SECRET=SECRET_HERE
NODE_ENV=ENV_TYPE_HERE
```