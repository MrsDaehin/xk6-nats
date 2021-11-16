# xk6-nats

This is a [k6](https://go.k6.io/k6) extension using the [xk6](https://github.com/k6io/xk6) system, that allows to use NATS protocol.

| :exclamation: This is a proof of concept, isn't supported by the k6 team, and may break in the future. USE AT YOUR OWN RISK! |
|------|

## Build

To build a `k6` binary with this extension, first ensure you have the prerequisites:

- [Go toolchain](https://go101.org/article/go-toolchain.html)
- Git

1. Install `xk6` framework for extending `k6`:
```shell
go install go.k6.io/xk6/cmd/xk6@latest
```

2. Build the binary:
```shell
xk6 build --with github.com/ydarias/xk6-nats@latest
```

3. Run a test
```shell
./k6 run folder/test.js
```

## Test structure

```javascript
import {check, sleep} from 'k6';
import {Nats} from 'k6/x/nats';

const natsConfig = {
    servers: ['nats://localhost:4222'],
    unsafe: true,
};

const publisher = new Nats(natsConfig);
const subscriber = new Nats(natsConfig);

export function setup() {
}

export default function () {
    // Subscribing to a topic
    subscriber.subscribe('topic', (msg) => {
        check(msg, {
            'Is expected message': (m) => m.data === 'the message',
            'Is expected topic': (m) => m.topic === 'topic',
        })
    });

    sleep(1)

    // Publising in a topic
    publisher.publish('topic', 'the message');

    sleep(1)
}

export function teardown() {
    publisher.close();
    subscriber.close();
}
```

### Configuration options

```json
{
  "servers": [
    "nats://localhost:4222"
  ],
  "unsafe": false,
  "token": "token-value"
}
```

* `servers` accepts an array of strings with the URL to the NATS servers.
* `unsafe` (optional) allows to run with self-signed certificates when doing tests against `localhost` configured with a certificate, if the value is `true` (default value is `false`)
* `token` (optional) is the value of the token used to connect to the NATS server.