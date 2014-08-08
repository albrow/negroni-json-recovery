JSON Recovery middleware for negroni
-------------------------------------

[Negroni](https://github.com/codegangsta/negroni) is an idiomatic approach to web middleware in Go.

This recovery middleware catches any panics and wraps them up into a json response, including
the line number where the panic occured. Borrows heavily from the default
[recovery middleware in martini](https://github.com/go-martini/martini/blob/master/recovery.go).

See also https://github.com/go-martini/martini/blob/master/LICENSE

Usage
-----

### Installation

Installation is the same as any other package
```
go get github.com/albrow/negroni-json-recovery
```

Make sure you have imported the package:

```go
import "github.com/albrow/negroni-json-recovery"
```

Then add to the middleware stack:

```go
n.Use(recovery.JSONRecovery(true))
```

In production, you should set fullMessages to false:
```go
n.Use(recovery.JSONRecovery(false))
```

### Full Example

```go
package main

import (
	"github.com/albrow/negroni-json-recovery"
	"github.com/codegangsta/negroni"
	"net/http"
)

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
		panic("Oh no! Something went wrong")
	})

	n := negroni.New(negroni.NewLogger())
	n.Use(recovery.JSONRecovery(true))
	n.UseHandler(mux)
	n.Run(":3000")
}
```

The above code will render the following response:

```json
{
    "Code": 500,
    "Short": "internalError",
    "Errors": [
        "Oh no! Something went wrong"
    ],
    "From": "/Users/alex/programming/go/src/github.com/albrow/testing/negroni-panic/server.go:12"
}
```

You can also inspect console for a more detailed message and full stack trace.