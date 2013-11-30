# apostle.js


Node.js and JavaScript bindings for Apostle.io.

You can use this library to send emails via Apostle.io from both the server and the client. The Apostle.io delivery API supports Cross Origin Resource Sharing (CORS).

## Installation

### Installing via NPM

```
npm install apostle
```

In your code

```
var apostle = require("apostle");
```

### Installing via Bower

```
bower install apostle
```

### Installing manually

Download the latest code from GitHub, and include `lib/index.js`.


## Usage

### Domain Key

You will need to provide your apostle domain key to send emails.

```js
apostle.domainKey = "Your domain key";
```

### Sending Email

Sending an email is easy, a minimal example may look like this

```
apostle.mail('welcome_email', {email: 'mal@apostle.io'});
```

You can pass any information that your Apostle.io template might need.


```
var order = {
	items: ['Widget frame', 'Widget chain', 'Widget seat'],
	id: "abc123"
};

apostle.mail('order_complete', {
	email: 'mal@apostle.io',
	replyTo: 'support@apostle.io',
	order: order
});
```

You can provide a callback for success, validation failure and delivery failure.

* In the case of success, only the success flag will be supplied.
* In the case of invalid parameters being passed, no external request will be made and all parameters will be supplied. The message will be 'invalid', and the response object will be an array of mail messages with an error property.
* In the case of deliver failure, all parameters will be supplied. The message param will be set to 'error', and the response object will be a [Superagent Response Object](http://visionmedia.github.io/superagent/#response-properties). See below for error status codes and their meanings.


```
var callback = function(success, message, response){};

// Invalid Template
apostle.mail(false, {email: 'mal@apostle.io'}, callback);
/**
 * Callback will receive
 * success: false
 * message: 'invalid'
 * response: [{ email: 'mal@apostle.io', error: 'No template provided'}]
 */
 
// Invalid Email
apostle.mail('welcome_email', {}, callback);
/**
 * Callback will receive
 * success: false
 * message: 'invalid'
 * response: [{ template_id: 'welcome_email', error: 'No email provided'}]
 */

// In the case of a server error
apostle.mail('welcome_email', {email: 'mal@apostle.io'}, callback);
/**
 * Callback will receive
 * success: false
 * message: 'error'
 * response: Superagent Response Object
 */
 
// Success
apostle.mail('welcome_email', {email: 'mal@apostle.io'}, callback);
/**
 * Callback will receive
 * success: true
 * message: undefined
 * response: undefined
 */

```

### Sending multiple emails

You can send multiple emails at once by using a queue. If any of the emails fail validation, no emails will be sent.

```
var queue = apostle.createQueue();

queue.add('welcome_email', {email: 'mal@apostle.io'});
queue.add('order_email', {email: 'mal@apostle.io', order: order})

queue.deliver(callback);
```

### Failure Responses

When recieving a callback with `success == false && message == 'error'`, it means that the delivery to Apostle.io has failed. There are several circumstances where this might occur. You should check the `response.status` value to determine your next action. Any 2xx status code is considered a success, and will not return a `success` value of false. Shortcut methods are available for some responses. In all cases, except a server error,  you can check `response.body.message` for more information.

* `response.unauthorized`, `response.status == 401` – Authorization failed. Either no domain key, or an invalid domain key was supplied.
* `response.badRequest`, `response.status == 400` – Either no json, or invalid json was supplied to the delivery endpoint. This should not occur when using the library correctly.
* `response.status == 422` – Unprocessable entitity. An invalid payload was supplied, usually a missing email or template id, or no recipients key. `Apostle.js` should validate before sending, so it is unlikely you will see this response.
* `response.serverError`, `response.status == 500` – Server error occured. Something went wrong at the Apostle API, you should try again with exponential backoff.



## Who
Created with ♥ by [Mal Curtis](http://github.com/snikch) ([@snikchnz](http://twitter.com/snikchnz))


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request







