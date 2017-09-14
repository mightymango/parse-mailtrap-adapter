# Parse Mailtrap Adapter

With this adapter you can send email for reset password and email verification in parse with SMTP access and custom templates, I am doing methods for support email verification, and templates for reset password pages :)

### Installation

Install npm module in your parse server project

```sh
$ npm install --save parse-mailtrap-adapter
```

### Use

In the configuration of your parse server you must pass `parse-mailtrap-adapter` as email adapter and set your SMTP access for send emails also the path to your jade template and its less file.

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
			fromAddress: 'your@sender.address',
			user: 'Your mailtrap username',
			password: 'Your mailtrap password', 
			//Somtimes the user email is not in the 'email' field, the email is search first in
			//email field, then in username field, if you have the user email in another field
			//You can specify here
			//emailField: 'username', 
			// Verification email subject
			      verificationSubject: 'Please verify your e-mail for *|appname|*',
			      // Verification email body. This will be ignored when verificationTemplateName is used.
			      verificationBody: 'Hi *|username|*,\n\nYou are being asked to confirm the e-mail address *|email|* with *|appname|*\n\nClick here to confirm it:\n*|link|*',
			// Password reset email subject
			      passwordResetSubject: 'Password Reset Request for *|appname|*',
			      // Password reset email body. This will be ignored when passwordResetTemplateName is used.
			      passwordResetBody: 'Hi *|username|*,\n\nYou requested a password reset for *|appname|*.\n\nClick here to reset it:\n*|link|*',
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
The path you pass to the email adapter must be a directory and not a file, this path must contain 2 mandatory files `html.jade` and `style.less` you can do your template as you like with the [CSS rules that emails supports](https://www.campaignmonitor.com/css/) in the template you can use 3 variables:

- appName //This is the name of your parse app
- link //This is the link for reset the password
- user //This is a Parse object with the current user, so you can use any field in your User class of parse for example the user name `#{user.get('username')}`

### Contributing
This module is pull request friendly in the develop branch feel free of send new features or bug fixes.

If you find a bug please open an issue.

### License MIT
