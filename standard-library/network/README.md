# `network`

```
import "network" as network
```

Provides TCP networking support, including TCP servers, clients, sending and receiving data, and socket configuration.

## Socket errors

Network failures raise `network.SocketError`.

```
try do
    let client = network.tcp_client("example.com", 80)
catch network.SocketError as e do
    term.log("Connection failed")
```

## `network.tcp(port)`

Creates a TCP server socket listening on the specified port.

```
let server = network.tcp(8080)
```

Connections can be accepted using `accept()`:

```
let connection = server.accept()
```

## `network.tcp.accept()`

Waits for and accepts an incoming TCP connection.

Returns a `tcp_connection` object.

```
let server = network.tcp(8080)

let client = server.accept()
```

## `network.tcp_client(host, port)`

Connects to a TCP server.

```
let client = network.tcp_client("localhost", 8080)
```

The returned object can send and receive data.

## Sending and receiving data

### `send(buffer)`

Sends raw data through the socket.

```
client.send(buffer)
```

Returns the number of bytes sent.

### `send_string(string)`

Sends a string through the socket.

```
client.send_string("Hello, server!")
```

Returns the number of bytes sent.

### `recv(buffer)`

Receives data into a buffer.

```
client.recv(buffer)
```

Returns the number of bytes received.

### `recv_string(size)`

Receives up to `size` bytes and returns them as a string.

```
let message = client.recv_string(1024)
```

### `peek(buffer)`

Reads data without removing it from the socket receive buffer.

```
client.peek(buffer)
```

## Polling

Sockets can be checked for readiness using `poll()`.

### `poll(want_read, want_write, timeout_ms)`

Waits until a socket is ready for reading or writing.

Returns a bitmask containing one or more of:

| Constant | Value | Meaning |
| --- | --- | --- |
| `NET_POLL_READ` | `1` | Socket is ready for reading |
| `NET_POLL_WRITE` | `2` | Socket is ready for writing |

A return value of `0` indicates that the timeout expired.

A return value of `-1` indicates an error.

Example:

```
let result = client.poll(true, false, 1000)

if (result & network.NET_POLL_READ) do
    let data = client.recv_string(1024)
```

A timeout of `-1` blocks indefinitely.

## Non-blocking sockets

### `set_nonblocking(enable)`

Enables or disables non-blocking mode.

```
client.set_nonblocking(true)
```

When enabled, socket operations return immediately instead of waiting.

## Socket options

### `set_opt(option, value)`

Sets a socket option.

Available options:

| Constant | Meaning |
| --- | --- |
| `NET_OPT_KEEPALIVE` | Enable TCP keepalive |
| `NET_OPT_NODELAY` | Disable Nagle's algorithm |
| `NET_OPT_REUSEADDR` | Allow address reuse |
| `NET_OPT_RCVTIMEO` | Set receive timeout |
| `NET_OPT_SNDTIMEO` | Set send timeout |

Example:

```
client.set_opt(network.NET_OPT_NODELAY, true)
```

## Closing sockets

### `close()`

Closes the socket and releases its resources.

```
client.close()
server.close()
```

## Example TCP server

```
let server = network.tcp(8080)

while true do
    let connection = server.accept()

    let message = connection.recv_string(1024)
    connection.send_string("Received: " + message)

    connection.close()
```

## Example TCP client

```
let client = network.tcp_client("localhost", 8080)

client.send_string("Hello!")

let response = client.recv_string(1024)

term.log(response)

client.close()
```

## Next

- [`time`](../time/README.md)
- [`date`](../date/README.md)