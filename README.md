# tubetunnel

tubetunnel exposes your localhost to the world for easy testing and sharing! No need to mess with DNS or deploy just to have others test out your changes.

Great for working with browser testing tools like browserling or external api callback services like twilio which require a public url for callbacks.

## Quickstart

```
npx tubetunnel --port 8000
```

## Installation

### Globally

```
npm install -g tubetunnel
```

### As a dependency in your project

```
yarn add tubetunnel
```

## CLI usage

When tubetunnel is installed globally, just use the `lt` command to start the tunnel.

```
lt --port 8000
```

Thats it! It will connect to the tunnel server, setup the tunnel, and tell you what url to use for your testing. This url will remain active for the duration of your session; so feel free to share it with others for happy fun time!

You can restart your local server all you want, `tubetunnel` is smart enough to detect this and reconnect once it is back.

### Arguments

Below are some common arguments. See `tubetunnel --help` for additional arguments

- `--subdomain` request a named subdomain on the tubetunnel server (default is random characters)
- `--local-host` proxy to a hostname other than localhost

You may also specify arguments via env variables. E.x.

```
PORT=3000 lt
```

## API

The tubetunnel client is also usable through an API (for test integration, automation, etc)

### tubetunnel(port [,options][,callback])

Creates a new tubetunnel to the specified local `port`. Will return a Promise that resolves once you have been assigned a public tubetunnel url. `options` can be used to request a specific `subdomain`. A `callback` function can be passed, in which case it won't return a Promise. This exists for backwards compatibility with the old Node-style callback API. You may also pass a single options object with `port` as a property.

```js
const tubetunnel = require('tubetunnel');

(async () => {
  const tunnel = await tubetunnel({ port: 3000 });

  // the assigned public url for your tunnel
  // i.e. https://abcdefgjhij.tunnel.bittube.app
  tunnel.url;

  tunnel.on('close', () => {
    // tunnels are closed
  });
})();
```

#### options

- `port` (number) [required] The local port number to expose through tubetunnel.
- `subdomain` (string) Request a specific subdomain on the proxy server. **Note** You may not actually receive this name depending on availability.
- `host` (string) URL for the upstream proxy server. Defaults to `https://tunnel.bittube.app`.
- `local_host` (string) Proxy to this hostname instead of `localhost`. This will also cause the `Host` header to be re-written to this value in proxied requests.
- `local_https` (boolean) Enable tunneling to local HTTPS server.
- `local_cert` (string) Path to certificate PEM file for local HTTPS server.
- `local_key` (string) Path to certificate key file for local HTTPS server.
- `local_ca` (string) Path to certificate authority file for self-signed certificates.
- `allow_invalid_cert` (boolean) Disable certificate checks for your local HTTPS server (ignore cert/key/ca options).

Refer to [tls.createSecureContext](https://nodejs.org/api/tls.html#tls_tls_createsecurecontext_options) for details on the certificate options.

### Tunnel

The `tunnel` instance returned to your callback emits the following events

| event   | args | description                                                                          |
| ------- | ---- | ------------------------------------------------------------------------------------ |
| request | info | fires when a request is processed by the tunnel, contains _method_ and _path_ fields |
| error   | err  | fires when an error happens on the tunnel                                            |
| close   |      | fires when the tunnel has closed                                                     |

The `tunnel` instance has the following methods

| method | args | description      |
| ------ | ---- | ---------------- |
| close  |      | close the tunnel |

## server

See [tubetunnel/server](//github.com/ipbc-dev/tubetunnel-server) for details on the server that powers tubetunnel.

## License

MIT
