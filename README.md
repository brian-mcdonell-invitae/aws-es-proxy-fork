# aws-es-proxy-fork

Fork of [abutaha/aws-es-proxy](https://github.com/abutaha/aws-es-proxy) with a `-disable-http2` flag that turns off HTTP/2 on the outbound HTTPS client. HTTP/2 is **disabled by default** in this fork.

## Why

Upstream's `http.Client` inherits Go's default transport, which auto-negotiates HTTP/2 via ALPN when talking to HTTPS endpoints. Some environments need HTTP/1.1 only (e.g. certain load balancer / TLS-terminator combinations that mishandle HTTP/2 stream resets). This fork exposes a knob to force HTTP/1.1.

## Usage

Identical to upstream, with two added flags:

```
-disable-http2
    Disable HTTP/2 on the outbound HTTPS client (default true)

-verbose-exclude-regex string
    When -verbose is set, suppress the per-request log line for requests
    whose URI matches this regex (e.g. '/_bulk|/_msearch')
```

To restore upstream behavior (HTTP/2 enabled), pass `-disable-http2=false`.

Example: log every request except `_bulk` and `_msearch`:

```
aws-es-proxy -endpoint=... -verbose -verbose-exclude-regex='/_bulk|/_msearch'
```

## Building

```sh
go build -o aws-es-proxy
```

Or via Docker:

```sh
docker build -t aws-es-proxy-fork:local .
```

## Container image

Pushed to internal ECR:

```
602328237953.dkr.ecr.us-west-2.amazonaws.com/utils/aws-es-proxy-fork:<version>
```

## License

Apache 2.0, inherited from upstream. See `LICENSE`.
