# dm-go

Reusable packages and frameworks for Go services

## Installation

```bash
go get github.com/delivery-much/dm-go
```

## Packages

Implemented packages in project, their use and some examples.

### Logger

Package for log application, ready for production (JSON) and development. Construct the log with some parameters: if is json, the level and some base fields that will appear in all output logs. Using the Sugar [zap](https://github.com/uber-go/zap) package.

Level: `debug`, `info` (default), `warn`, `error`, `fatal`

Example:

```go
config := logger.Configuration{
    IsJSON: true,
    Level:  "info",
    BaseFields: logger.BaseFields{
        ServiceName: "default-service",
        CodeVersion: "1.0.0",
        Env:         "production",
    },
}
err := logger.NewLogger(config)
if err != nil {
    panic(err)
}

logger.Infow("failed to fetch URL",
    // Key and value after the message
    "url", "www.google.com",
    "attempt", 3,
    "backoff", time.Second,
)
```

### Middleware

Package with some middleware for routes and service.

Example:

```go
// Middleware for generate or inject request id in context of request.
router.Use(
    middleware.RequestID("Key-Request-Id"),
)
```

### New Relic

Package with some helpers/middleware for send events to New Relic.

Example:

```go
InitNewRelic("app-teste", "1234")

// Func for database monitoring
newRelicSegment := StartNewRelicDBSegment(ctx.Background(), "select", "user")
defer newRelicSegment()

// Middleware for instruments handler functions using transactions and monitoring the route 
router.Post(newrelic.WrapHandleFunc("/test", h.handleTest()))
```

### Render

Package with some helpers for render responses. To respond in JSON format.

### String Utils

Package with some utility functions for string transformation

#### MaskString(str string) string
- MaskString masks the last half of a given string, changing any letter or number character to '*'
Example:
```golang
MaskString("examplestring") // returns "exampl*******"
MaskString("") // returns ""
```

#### MaskEmail(email string) string
- MaskEmail masks an email string,
leaving only the first four letters of the email id (i.e the part before the '@') and the email domain unmasked.
if the email id has 4 or less characters, leaves only 1 character unmasked.
Example:
```golang
MaskEmail("email_id@domain.com") // returns "emai*_**@domain.com"
MaskEmail("0101@domain.com") // returns "0***@domain.com"
MaskEmail("notanemail.com") // returns "notanemail.com"
MaskEmail("") //returns ""
```
