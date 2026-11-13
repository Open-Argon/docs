# `network`

```argon
import "network" as network
```

TCP sockets, TLS client sockets and an HTTP/HTTPS client.

## HTTP client

`network.http` provides `get`, `post`, `put`, `del` and `request`:

```argon
import "network" expose http

let resp = http.get("https://isotope.wbell.dev/")
term.log(resp.status, resp.ok())   # 200 true
term.log(resp.headers.value_get("Content-Type"))
let body = resp.text()
```

URLs can use `http://` or `https://` and may include a port (`http://localhost:8080/api`).

| Function | Description |
|---|---|
| `http.get(url, headers=null, ...)` | send a `GET` request |
| `http.post(url, body=null, headers=null, ...)` | send a `POST` request |
| `http.put(url, body=null, headers=null, ...)` | send a `PUT` request |
| `http.del(url, headers=null, ...)` | send a `DELETE` request |
| `http.request(method, url, headers=null, body=null, ...)` | send a request with any method |

Every function also accepts these keyword arguments:

| Argument | Default | Description |
|---|---|---|
| `verify_peer` | `true` | verify the server's TLS certificate |
| `ca_path` | `null` | path to a CA certificate bundle to verify against |
| `timeout_ms` | `30000` | how long to wait for data |
| `max_header_size` | `65536` | maximum response header size in bytes |
| `auto_close` | `true` | close the connection once the body has been read |

`headers` is a dictionary. `body` may be a string, a buffer, a `multipart_form` (see below),
or any iterable that yields buffers. A `Content-Length` header is added automatically for
strings, buffers and forms, and `Connection: close` is sent unless you set `Connection`
yourself.

```argon
import "network" expose http
import "json" as json

let resp = http.post(
    "https://example.com/api/items",
    body = json.stringify({name: "widget"}),
    headers = {"Content-Type": "application/json"},
)
```

### Responses

| Member | Description |
|---|---|
| `status` | the status code, e.g. `404` |
| `status_text` | the reason phrase, e.g. `"Not Found"` |
| `headers` | a dictionary of response headers (names as sent by the server) |
| `ok()` | `true` for a 2xx status |
| `text()` | the whole body as a string |
| `json()` | the body parsed as JSON |
| `bytes()` | the whole body as a buffer |
| `content_length()` | the `Content-Length` header as a number, or `null` |
| `chunk()` | the next chunk of the body as a buffer, or `null` at the end |
| `close()` | close the connection early |

A response is also iterable, yielding the body in buffer chunks. This lets you stream large
downloads to disk without holding them in memory:

```argon
import "network" expose http
import "file" expose open

let resp = http.get("https://isotope.wbell.dev/")
let out = open("page.html", "wb")
for (chunk in resp) out.write(chunk)
out.close()
```

The body can only be read once.

Network errors throw `network.HTTPError` (for timeouts and broken responses),
`network.SocketError` (when the connection fails) or `network.TLSError`. A non-2xx status
does **not** throw; check `ok()` or `status`.

### Multipart forms

`network.multipart_form` builds a `multipart/form-data` body, for example for file uploads.
File contents are streamed from disk while sending, not loaded into memory:

```argon
import "network" expose http, multipart_form
import "file" expose open

let form = multipart_form()
form.field("name", "Ada")
form.file("avatar", "photo.jpg", open("photo.jpg", "rb"))

let resp = http.post(
    "https://example.com/upload",
    body = form,
    headers = {"Content-Type": form.content_type()},
)
```

`form.file(field_name, filename, file, content_type=null)` takes ownership of the open file
and closes it once it has been sent. If `content_type` is omitted, it is guessed from the
file name's extension. Pass the form's `content_type()` (which includes the boundary) as the
`Content-Type` header.

## TCP

### Servers: `network.tcp(port)`

Listens on `port` on all interfaces. `accept()` waits for a client and returns a
connection:

```
import "network" as network

let server = network.tcp(8080)
while (true) do
    let conn = server.accept()
    let message = conn.recv_string(1024)
    conn.send_string("Received: " + message)
    conn.close()
```

### Clients: `network.tcp_client(host, port)`

```
import "network" as network

let client = network.tcp_client("localhost", 8080)
client.send_string("Hello!")
term.log(client.recv_string(1024))
client.close()
```

### Socket methods

Connections returned by `accept()` and clients created with `tcp_client` share these methods:

| Method | Description |
|---|---|
| `send(buffer)` | send bytes; returns the number sent |
| `send_string(string)` | send a string; returns the number of bytes sent |
| `recv(buffer)` | receive into `buffer`; returns the number of bytes received (`0` when the other side has closed) |
| `recv_string(size)` | receive up to `size` bytes and return them as a string |
| `peek(buffer)` | like `recv`, but leaves the data in the socket |
| `poll(want_read, want_write, timeout_ms)` | wait until the socket is ready (see below) |
| `set_nonblocking(enable)` | make operations return immediately instead of waiting |
| `set_opt(option, value)` | set a socket option (see below) |
| `close()` | close the socket |

Servers support `accept()`, `poll`, `set_nonblocking`, `set_opt` and `close()`.

Connection failures throw `network.SocketError`:

```argon
import "network" as network

try network.tcp_client("127.0.0.1", 1)
catch (network.SocketError as e) term.log(e.message)   # failed to connect to 127.0.0.1:1
```

### Polling

`poll(want_read, want_write, timeout_ms)` waits until the socket is ready, and returns a
bitmask of `network.NET_POLL_READ` (`1`) and `network.NET_POLL_WRITE` (`2`). It returns `0`
if the timeout expired and `-1` on error. A timeout of `-1` waits forever.

```
let ready = client.poll(true, false, 1000)
if (ready > 0) term.log(client.recv_string(1024))
else if (ready == 0) term.log("timed out")
```

### Socket options

| Constant | Meaning |
|---|---|
| `network.NET_OPT_KEEPALIVE` | enable TCP keepalive |
| `network.NET_OPT_NODELAY` | disable Nagle's algorithm |
| `network.NET_OPT_REUSEADDR` | allow reusing the address |
| `network.NET_OPT_RCVTIMEO` | receive timeout |
| `network.NET_OPT_SNDTIMEO` | send timeout |

```
client.set_opt(network.NET_OPT_NODELAY, true)
```

## TLS

`network.tls_client(host, port, verify_peer=true, ca_path=null)` opens an encrypted
connection. It supports `send`, `send_string`, `recv`, `recv_string`, `poll` and `close`.
`network.TLS_SUPPORTED` is `false` if the interpreter was built without TLS support, in which
case creating a TLS client throws `network.TLSError`.

## Next

- [`url`](../url/README.md)
