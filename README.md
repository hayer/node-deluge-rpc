# Node.js Deluge RPC Socket

Node.js API for Deluge's RPC API

## Setup

```bash
yarn add deluge-rpc-socket
```

## Usage

```js
const tls = require('tls');
const DelugeRPC = require('deluge-rpc-socket').default;

const socket = tls.connect(
  58846,
  {
    // Deluge often runs with self-signed certificates
    rejectUnauthorized: false,
  }
);

const rpc = DelugeRPC(socket);

let { result, sent } = rpc.daemon.login('username', 'password');

// Monitor socket status
sent
  .then(() => {
    console.log('Message sent');
  })
  .catch(console.error);

// Responses are resolved. Error responses are rejections.
result.then(console.log).catch(console.error);

// Listen for asynchronous events from daemon
rpc.events.on('delugeEvent', console.log);

// Non fatal decoding errors that indicate something is wrong with the protocol...
rpc.events.on('decodingError', console.log);
```

### Alternate API: Don't throw on error responses

```js
const alt = DelugeRPC(socket, { resolveErrorResponses: true });

let { result, sent } = rpc.request('daemon.info');

sent.then(socketError => {
  console.log(socketError || 'Message sent');
});

result.then(({ error, response }) => {
  console.log(error || response);
});
```

### Arguments

All arguments to API functions at any depth can be Promises.

#### camelCase vs snake_case

All of Deluge's arguments are snake_case.
Any named arguments will be converted to Deluge's snake_keys convention.

## Development

```bash
yarn setup
# Launch a REPL with `DelugeRPC` and `config` available in the context and useful commands in history
yarn start
```
