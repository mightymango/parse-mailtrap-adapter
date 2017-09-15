# Parse Mailtrap Adapter

With this adapter you can test emails sent for reset password and email verification in Parse with MailTrap.

It is based on [simple-parse-smtp-adapter](https://github.com/lcortess/simple-parse-smtp-adapter) by [Jose Luis](https://github.com/lcortess).

### Installation

Install npm module in your parse server project

```sh
$ npm install --save parse-mailtrap-adapter
```

### Use

In the configuration of your parse server you must pass `parse-mailtrap-adapter` as email adapter and set your MailTrap access and also the path to your jade template and its less file.

This is an example using parse server as express module:

```js
"use strict";

const Express = require('express');
const ParseServer = require('parse-server').ParseServer;

const app = Express();
const APP_PORT = 1337;

let options = {

};

let api = new ParseServer({
	appName: "Parse Test",
	appId: "12345",
	masterKey: "abcde12345",
	serverURL: "http://localhost:1337/parse",
	publicServerURL: "http://localhost:1337/parse",
	databaseURI: "mongodb://user:pass@host:27017/parse",
	port: APP_PORT,
	//This is the config for email adapter
	emailAdapter: {
		module: "parse-mailtrap-adapter",
		options: {
			host: 'smtp.mailtrap.io',
			port: 2525,
			fromAddress: 'your@sender.address',
			user: 'Your mailtrap username',
			password: 'Your mailtrap password', 
			templates: {
			    //This template is used only for reset password email
				resetPassword: {
				    //Path to your template
					template: __dirname + '/templates/email/reset-password',
					//Subject for this email
					subject: 'Reset your password'
				},
				verifyEmail: {
				    template: __dirname + '/templates/email/verify-email',
				    subject: 'Verify Email'
				}
			}
		}
	}
});

/**
 * Parse Server endpoint
 */
app.use('/parse', api);

app.listen(APP_PORT, function () {
	console.log(`Parse Server Ready and listening on port ${APP_PORT}`);
});
```

### Template
The path you pass to the email adapter must be a directory and not a file, this path must contain 2 mandatory files `html.jade` and `style.less`. In the template you can use 3 variables:

- #{appName} //This is the name of your parse app
- #{link} //This is the link for reset the password
- #{user} //This is a Parse object with the current user, so you can use any field in your User class of parse for example the user name `#{user.get('username')}`

#### Example Template
The following is an example of /templates/email/reset-password:

```html
<p>Hi #{user.get('firstname')},</p>
<p>You requested a password reset for #{appName}.</p>
<p>Click here to reset it:<br><a href="#{link}">#{link}</a></p>
```

`style.less` must exist, even if it is just an empty file.

### License MIT
