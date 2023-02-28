---
description: WoopChain API

---

# Access the WoopChain API

Access the [WoopChain API](../../reference/api/index.md) using:

* [JSON-RPC over HTTP, WebSocket, or IPC](json-rpc.md)
* [RPC Pub/Sub over WebSockets](rpc-pubsub.md)
* [GraphQL over HTTP](graphql.md).

The following sections provide information about JSON-RPC, RPC Pub/Sub, and GraphQL.

## Enable API access

To enable API access, use the
[`--rpc-http-enabled`](../../reference/cli/options.md#rpc-http-enabled),
[`--ws-http-enabled`](../../reference/cli/options.md#rpc-ws-enabled),
[`--graphql-http-enabled`](../../reference/cli/options.md#graphql-http-enabled), and
`--Xrpc-ipc-enabled` options.


## Service hosts

To specify the host the API service listens on, use the
[`--rpc-http-host`](../../reference/cli/options.md#rpc-http-host),
[`--rpc-ws-host`](../../reference/cli/options.md#rpc-ws-host), and
[`--graphql-http-host`](../../reference/cli/options.md#graphql-http-host) options. The
default host is `127.0.0.1`.

To allow remote connections, set the host to `0.0.0.0`.

!!! caution

    Setting the host to `0.0.0.0` exposes the API service connection on your node to any remote
    connection. In a production environment, ensure you use a firewall to avoid exposing your node
    to the internet.

## Service ports

To specify the port the API service listens on, use the
[`--rpc-http-port`](../../reference/cli/options.md#rpc-http-port),
[`--rpc-ws-port`](../../reference/cli/options.md#rpc-ws-port), and
[`--graphql-http-port`](../../reference/cli/options.md#graphql-http-port) options.

The default ports are:

* 8545 for JSON-RPC over HTTP.
* 8546 for JSON-RPC over WebSocket.
* 8547 for GraphQL over HTTP.


## Host allowlist

To prevent DNS rebinding attacks, Woop checks incoming HTTP request host headers, WebSocket connections, and GraphQL
requests.
Woop accepts requests only when hostnames specified using the
[`--host-allowlist`](../../reference/cli/options.md#host-allowlist) option matches the request host headers.
By default, Woop accepts requests and connections from `localhost` and `127.0.0.1`.

!!! important

    This isn't a permissioning feature.
    To restrict access to the API, we recommend using the [Woop authentication mechanism](authenticate.md)
    with username and password authentication or JWT public key authentication.

If your application publishes RPC ports, specify the hostnames when starting Woop.

!!! example

    ```bash
    woop --host-allowlist=example.com
    ```

Specify `*` for `--host-allowlist` to effectively disable host protection.

!!! caution

    Specifying `*` for `--host-allowlist` is not recommended for production code.

## Not supported by Woop

### Account management

Account management relies on private key management in the client, which is not supported by Woop.

To send signed transactions, use
[`eth_sendRawTransaction`](../../reference/api/index.md#eth_sendrawtransaction).
`eth_sendTransaction` is not implemented.


### Protocols

Woop does not support the Whisper and Swarm protocols.
