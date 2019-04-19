# Nexus Examples

## Simple Example

The `simple` example contains a very simple websocket server, and simple websocket subscriber and publisher clients.  This is a good place to start seeing how to build WAMP servers and clients using nexus.

The simple examples can be run from the `examples` directory by running:

1. Run the server with `go run simple/server.go`
2. Run the subscriber with `go run simple/sub/subscriber.go`
3. Run the publisher with `go run simple/pub/publisher.go`

## Example Server and Clients

The project Wiki provides a walk-through of [client](https://github.com/quanticko/nexus/wiki/Client-Library) and [server](https://github.com/quanticko/nexus/wiki/Router-Library) example code.

The example server, in the `server` directory, runs a websocket (with and without TLS), tcp raw socket (with and without TLS), and Unix raw socket transport at the same time.  This allows different clients to communicate with each other when connected to the server using any combination of socket types, TLS, and serialization schemes.

The example clients are located in the following locations:

- `pubsub/subscriber/`
- `pubsub/publisher/`
- `rpc/callee/`
- `rpc/caller/`
- `session_meta_api/`

When connecting a client, set the URL scheme with `-scheme=` to specify the type of transport, and whether or not to use TLS:

- Websocket: `-scheme=ws`
- Websocket + TLS: `-scheme=wss`
- TCP socket: `-scheme=tcp`
- TCP socket + TLS: `-scheme=tcps`
- Unix socket: `-socket=unix`

If no scheme is specified, then the default is `ws` (websocket without TLS).

When using TLS ("wss" or "tcps" schemes), certificate verification fails when using a certificate that cannot be verified.  For verification of the server's certificate to work, it is necessary to trust the server's certificate by specifying `-trust=server/cert.pem`.  Verification can also be skipped using the `-skipverify` flag.  Example running subscriber client:
```
go run pubsub/subscriber/subscriber.go -scheme=wss -trust=server/cert.pem
```

To choose the type of serialization for the client to use, specify `-serialize=json` or `-serialize=msgpack`.  If no serialization is specified, then the default is `json`.

NOTE: The certificate and key used by the example server were created using the following commands:
```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 3650
openssl rsa -in key.pem -out rsakey.pem
```

See `-help` option for additional options available for server and clients.

## RPC Example

The RPC example provides two callee clients.  One callee client is embedded in the server running the WAMP router, and the other is an external client.  The internal client does not require a socket or serialization and is run as part of the server.  The external client connects to the router using a socket. 

The external caller client makes calls to both the internal and the external callee clients.

### Caller and Callee Both External Clients

1. Run the server with `go run server/server.go`
2. Run the callee with `go run rpc/callee/callee.go`
3. Run the caller with `go run rpc/caller/caller.go`

## Pub/Sub Example

The pub/sub example provides a subscriber client and a publisher client that connect to the nexus server to demonstrate simple pub/sub messaging.

This pub/sub example does not have an internal client embedded in the router, as creating an embedded client is demonstrated by the internal RPC client.

### Run the Subscriber and Publisher Clients

1. Run the server with `go run server/server.go`
2. Run the subscriber with `go run pubsub/subscriber/subscriber.go`
3. Run the publisher with `go run pubsub/publisher/publisher.go`

## Session Meta API Example

The session meta API example provides a client that subscribes to session meta events and calls session meta procedures to demonstrate the session meta API.

### Run the Session Meta Client Example

1. Run the server with `go run server/server.go`
2. Run the client with `go run session_meta_api/session_meta_client.go`

## Multiple Transport Example

A nexus router is capable of routing messages between clients running with different transports and serializations.  To see this, you can run the example nexus server and then connect clients that use different combinations of websockets and raw sockets, and JSON and MsgPack serialization.

### Run Websocket Subscriber with TCP and Unix Raw Socket Publishers

1. Run the server with `go run server/server.go`
2. Run the subscriber with `go run pubsub/subscriber/subscriber.go`
3. Run a publisher with `go run pubsub/publisher/publisher.go -scheme=tcp`
4. Run a publisher with `go run pubsub/publisher/publisher.go -scheme=unix`

Try different combinations socket type, TLS, and serialization with multiple clients.  See `-help` for options.
