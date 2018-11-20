### Middlewars!

Typo intended! There are many ways to build middleware in Go, however, in practice it's often simplest to just bootstrap it yourself and with a single function, combine all of the middleware you can think up! This also assumes that you're using a pretty vanilla router, like Gorilla MUX in our case. 

### The `Use` Function

Our middleware foundation is based on a single key function, `Use`:

```go
// `Use` allows us to stack middleware to process the request
// Example taken from https://github.com/gorilla/mux/pull/36#issuecomment-25849172
func Use(handler http.HandlerFunc, mid ...func(http.Handler) http.HandlerFunc) http.HandlerFunc {
	for _, m := range mid {
		handler = m(handler)
	}
	return handler
}
```

While it might seem somewhat cryptic, all it does is 'stack' middleware (which are just `http.Handler` functions) on top of each other so that we can call them all with a single route. Note that this will stack our middleware in FILO (first in, last out) order -- this will become clearer in our following sections on routes.

Find it in our middleware lib [here](https://github.com/exlinc/golang-utils/blob/master/httpmiddleware/use.go)

### Recover Panics in Request Goroutine

Each of our HTTP calls is going to get handled in it's own goroutine, so we need to be careful about panics (runtime exceptions). For instance, let's imagine we try to dereference a nil pointer in our handler. If we don't have a `recover` somewhere above that handler in our middleware (running in the same goroutine), then our entire server will crash. Sad!

So we use a generic middleware function to recover (stop the panic before it reaches the top function in our goroutine's call stack). This function is `RecoverInternalServerError`:

```go
func RecoverInternalServerError(handler http.Handler) http.HandlerFunc {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		defer func() {
			r := recover()
			if r != nil {
				w.WriteHeader(http.StatusInternalServerError)
				w.Write([]byte("Internal server error"))
			}
		}()
		handler.ServeHTTP(w, r)
	})
}
```

Find it in our middleware lib [here](https://github.com/exlinc/golang-utils/blob/master/httpmiddleware/recover.go)
