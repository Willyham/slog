# `slog`
**S**tructured **log**ging.

slog is a library for capturing structured log information. In contrast to "traditional" logging libraries, slog:

* captures a [Context](https://golang.org/pkg/context/) for each event
* captures arbitrary key-value metadata on each log event

slog forwards messages to [`log`](https://golang.org/pkg/log/) by default. But you probably want to write a a custom output to make use of the context and metadata. At [Monzo](https://monzo.com/), slog captures events both on a per-service and a per-request basis (using the context information) and sends them to a centralised logging system. This lets us view all the logs for a given request across all the micro-services it touches.

## Usage

Internally at Monzo, we recommend that users always prefer structured logging where possible. An example of using slog for this would be:

```go
slog.Info(ctx, "Loading widget", map[string]interface{}{
    "stage": "reticulating splines",
})
```

When logging an error, we recommend attaching the actual error in the "error" key:

```go
err := errors.New("No splines to reticulate")
slog.Error(ctx, "Failed to load widget", map[string]interface{}{
    "error": err,
})
```

This lets allows for interoperability with other systems (e.g. one which also forwards errors to an error tracking system) without losing context. `slog` also provides a shorthand for capturing this case in the form of:

```go
err := errors.New("No splines to reticulate")
slog.Error(ctx, "Failed to load widget", err)
```

### Other uses

For backwards-compatibility, slog accepts metadata in the form of `map[string]string`.

It also accepts format parameters in the style of `Printf`:

```go
stage := "reticulating splines"
slog.Info(ctx, "Loading widget at stage: %s", stage)
```
