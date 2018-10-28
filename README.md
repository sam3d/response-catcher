# res-error-handler
_Easy async error handling in Express_

```console
npm install --save res-error-handler
```

### Introduction
`res-error-handler` is an extremely flexible `.catch()` error handler for promise-based express request flows.

It should be used where you would have multiple `.then()` operations in a request and need to be able to throw and handle many kinds of errors.

By default it attaches itself to `res.error()` (though this can be configured).

### Usage example

```javascript
const express = require("express");
const app = express();

const resErrorHandler = require("res-error-handler");
app.use(resErrorHandler());

app.get("/", (req, res) => {
	findUser()
		.then(checkUser)
		.then(andThenThisThing)
		.catch(res.error());

	async function findUser() {
		let user = await db.users.find(req.body.id);
		if (!user) throw 404;

		return user;
	}

	async function checkUser(user) {
		if (!user.isAuthenticated) throw [401, "User is not authenticated"];
		else if (user.alreadyExists) throw ["User already exists", 409];
		else if (user.username.length > 8) throw "Username is too long";
		else if (serverErrorSomewhere) throw { status: 500, body: "Sorry, we had an error somewhere!" };
	}

	async function andThenThisThing() {
		// ...
	}
});
```

## Configuration

### Instantiation
On instantiation of the middleware function, you can set the following properties (with the listed values being the defaults):

```javascript
{
	name: "error", // The name of the function to use: "res.error()"
	status: 400, // The default response status to use
	logger: null // The logging function to use for errors
}
```

To enable logging, just pass `console.log` (not `console.log()`) as the value to the `logging` key, as it will use that.

You can supply as many arguments as you like. Any string you provide will interpreted as the `name`, any number you use will be interpreted as the `status`, and any object you use will have the named properties.

```javascript
const resErrorHandler = require("res-error-handler");

app.use(resErrorHandler()); // Use the default values
app.use(resErrorHandler(405)); // Change the default status to 405
app.use(resErrorHandler({ logger: console.log })); // Enable logging
app.use(resErrorHandler("handleError", { status: 500 })); // Change both the default function name and the status
```

### Creating the error
_Coming soon..._

### Ways to throw an error
_Coming soon..._
