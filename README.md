# Structured Log

Package provides to structured log both for displaying it sanely in the stderr
logs as well as writing key-valued logs into ElasticSearch:

Using this package it's possible to use it with Goa as well and logs will be
displayed in the stderr like this:

```
2017-09-21 18:58:19 [DEBUG] {goa} completed
                            ├─ req_id: aIlGSFNnk8-1
                            ├─ status: 200
                            ├─ bytes: 134030
                            ├─ time: 1.288885343s
                            ├─ ctrl: MetricsController
                            └─ action: get
```

To setup basic stderr logging, use following snippet in your `main.go`:

```go
import "github.com/reconquest/structured-logger-go"

// ...

stderr := lorg.NewLog()
stderr.SetIndentLines(true)
stderr.SetFormat(
    lorg.NewFormat("${time} ${level:[%s]:right:short} ${prefix}%s"),
)

if args["--debug"].(bool) {
    stderr.SetLevel(lorg.LevelDebug)
}

log = logger.NewLogger(stderr)

// use like that
log.Infof(nil, "message to log: %d", 1)
log.Infof(karma.Describe("key", "value"), "message to log: %d", 1)

err := errors.New("some error")
log.Fatalf(
    karma.Describe("context", "testing error").Reason(err),
    "message to log: %d",
    1,
)
```

To see more examples of how to use `karma` for structured logging and error
reporting, consider [looking at tests and examples][1].

## Motivation

Following package offers significant improvenets above `logrus` and similar
structured loggers:

* Readable tree-like log entries in stderr, which makes easy to debug program,
  because log is more readable.
* Allows to use context errors, that used to describe errors on all call-stack
  levels to ease finding problems and fixing them.
* Sends logs to ES in key-value format like other structured loggers.
* Does not change ordering of key-values in the context.

[1]: https://github.com/reconquest/karma-go/blob/f802f635edd15c647995280b90f7de3e84ca8999/karma_test.go
