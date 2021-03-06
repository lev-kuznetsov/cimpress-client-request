cimpress-client-request
======
A module for handling generation of OAuth Bearer tokens issued by Auth0 by integrating credential management into request.js.

## Installation

```shell
npm install cimpress-client-request
```

### Usage

This module exposes a single method:

```js
module.exports.refresh = function(config, cb) {}
```

This works as a drop-in replacement for [request](https://github.com/request/request).  Adopting this flow is as simple as these two surgical incisions:

```js
//var request = require('request');
var request = require('cimpress-client-request');
```

```js
// Note the set of 4 possible new options that can be passed in the request.js options.auth object.
// Every other property in the request options object works as normal, and you can call all of the
// convenience methods exposed by request.js.
var options = {
    auth: {
        client_id: 'see below',
        client_secret: 'see below',
        refresh_token: 'see below',
        target_id: 'see below'
    }
};
request(options);
```

Here's how you should use those 4 `auth` parameters:

| Property | Description |
|---|---|
| client_id | The client id you wish to use to request client credential grants (https://auth0.com/docs/api-auth/grant/client-credentials). |
| client_secret | The client secret you wish to use to request client credential grants (https://auth0.com/docs/api-auth/grant/client-credentials). |
| refresh_token | A refresh token for use in delegation flows, retrieved from developer.cimpress.io.  Defaults to the environment variable `CIMPRESS_IO_REFRESH_TOKEN`.  |
| target_id | OPTIONAL The client id for which you are trying to retrieve a delegated token.  Note, if you don't know this, you can rely on a 401 with a `Www-Authenticate` to provide the client id.  If you don't provide this config, and the service doesn't provide that header, your call will fail with a 401. |
| authorization_server | OPTIONAL The server to call to request client credential grants  (https://auth0.com/docs/api-auth/grant/client-credentials).  This defaults to https://cimpress-dev.auth0.com/oauth/token.
| audience | OPTIONAL The audience to send when requesting client credential grants  (https://auth0.com/docs/api-auth/grant/client-credentials).  This defaults to https://api.cimpress.io/ |

### Tests
You might also want to [look at our tests](https://mcpstash.cimpress.net/projects/CE/repos/cimpress-client-request-node/browse/test) to see some examples of usage.

You can run tests via `grunt` or `grunt test`, but keep in mind that the tests are reliant on a few configurations that must be provided via environment variables:

| Variable  | Description  |
|---|---|
| CIMPRESS_IO_REFRESH_TOKEN | A refresh token retrieved from developer.cimpress.io. |
| CIMPRESS_IO_CLIENT_ID | The client id you wish to use to request client credential grants (https://auth0.com/docs/api-auth/grant/client-credentials). |
| CIMPRESS_IO_CLIENT_SECRET | The client secret you wish to use to request client credential grants (https://auth0.com/docs/api-auth/grant/client-credentials). |
| CIMPRESS_IO_TARGET_ID | The target ID used for delegation flows |
| API_THAT_SUPPORTS_CLIENT_GRANTS | The URL for any `GET` endpoint on an API that supports client credential grants (https://auth0.com/docs/api-auth/grant/client-credentials). |
| API_THAT_SUPPORTS_DELEGATION | The URL for any `GET` endpoint on an API that supports delegated tokens (https://auth0.com/docs/api-auth/grant/client-credentials). |

## Development

### Install Nodejs & Grunt

```shell
sudo apt-get install nodejs
```

If you are running Ubuntu you need to create a symlink for node. (There is a naming conflict with the node package).

```shell
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Install Grunt command line tool:

```shell
sudo npm -g grunt-cli
```

If you see an error about NPM not installed, run the following command to install:

```shell
sudo apt-get install npm
```

### Getting Started from Source

```shell
cd src/
sudo npm install
grunt
```

### Package & Install without pushing to a remote NPM registry

To package:

```shell
npm pack
```

This will generate a tarball gzipped following a file name convention of: &lt;module&gt;-&lt;version&gt;.tgz

To install:

```shell
npm install <path-to-tgz>
```

### git - pre-commit hook

If you would like to run jslint and units tests before your commit then create a file in .git/hooks/pre-commit with execute permissions with the following content:

```shell
#!/bin/sh

grunt --gruntfile $(git rev-parse --show-toplevel)/src/Gruntfile.js
```