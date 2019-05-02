# node-pxgrid

<p align="center">
  <img src="https://travis-ci.org/rnwolfe/node-pxgrid.svg?branch=master" alt="Build Information" />
  <img src="https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=flat-square" alt="Code Style: Prettier" />
</p>

This is a Node.js module for interacting with Cisco PxGrid 2.0 that uses REST and WebSockets along with a STOMP-based messaging protocol. You can read more about how pxGrid works [on Cisco DevNet](https://developer.cisco.com/docs/pxgrid/#!introduction-to-pxgrid-2-0).

It has great performance improvements over using Cisco ISE's standard REST API as well as PxGrid 1.0, as well as the obvious benefits that come with a subscribe/publish model over a general pull model.

## Documentation

[Please view the documentation here](https://rnwolfe.github.io/node-pxgrid/).

Feel free to open an issue or otherwise contact me if you feel the documentation could be improved upon.

## Getting Started

### Installing

Install using `npm`:

```bash
npm i node-pxgrid
# or save
npm i node-pxgrid --save
```

### Creating a pxGrid account on Cisco ISE

In order to get started, you will need to create a pxGrid account on Cisco ISE. To my knowledge, Cisco ISE is the only pxGrid controller that exists today, but the package should, theoretically, support any other accurate implementation of pxGrid 2.0. This involves getting a certificate for authenticating your client. [Please follow the steps here to create an account](https://github.com/rnwolfe/node-pxgrid/blob/master/pxgrid-setup.md).

After you have an account, you can use it in your app. Note, that the initial time it registers, it will need to be approved in the pxGrid controller. If you used the above guide, this will be done automatically. The `Control.activate()` function will automatically retry every 30 seconds if it's initial state is pending. Sometimes, the auto-approval may take 1 try before completing.

### Using in an App

```javascript
const fs = require('fs');
const Pxgrid = require('node-pxgrid');

certs = [];
certs.certPath = './certs/';
certs.clientCert = fs.readFileSync(certs.certPath + 'my-node-app.cer');
certs.clientKey = fs.readFileSync(certs.certPath + 'my-node-app.key');
certs.caBundle = fs.readFileSync(certs.certPath + 'ise-chain.cer');

const pxgridControlOptions = {
  host: 'dnaise.ironbowlab.com',
  client: 'my-node-app',
  clientCert: certs.clientCert,
  clientKey: certs.clientKey,
  caBundle: certs.caBundle,
  clientKeyPassword: 'Pxgrid123'
};

const control = new Pxgrid.Control(pxgridControlOptions);
const client = new Pxgrid.Client(control);

control.activate().then(() => {
  client.connectToBroker().then(session =>
    client.subscribeToSessions(session, function(message) {
      console.log(message.body);
    })
  );
});
```

For a full list of functions, please see [the documentation](https://rnwolfe.github.io/node-pxgrid/). For more example usage, see the [examples](examples/).

## Bugs

For bugs, [please open an issue](https://github.com/rnwolfe/node-pxgrid/issues).

## License

This module is licensed under the [MIT license](LICENSE).
