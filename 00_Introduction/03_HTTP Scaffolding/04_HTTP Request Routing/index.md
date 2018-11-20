### Routing HTTP Requests

Now that we have our middleware, controllers, and a few other boilerplate features, we can finally create our routing infrastructure that will combine our middleware with our controllers that will actually respond to HTTP requests!

### Gorilla MUX

To simplify this process and get some 'freebies' like automatic responses to browser `OPTIONS` requests, etc., we're going to pull in the Gorilla MUX router. You can read more about it [here](https://github.com/gorilla/mux) and it has become somewhat of a de-facto standard when it comes to building web services in Go.

### Our Router

In `routes/routes.go` we're going to define our routes, connect the middleware, and export an HTTP handler that we'll then use in `main.go` to close out the loop! Here's what we're going to have in `routes/routes.go`:

```go
package routes

import (
	"github.com/exlinc/golang-utils/httpmiddleware"
	"github.com/exlskills/demo-go-webservice/config"
	"github.com/exlskills/demo-go-webservice/controllers/v1"
	"github.com/exlskills/demo-go-webservice/middleware"
	"github.com/gorilla/mux"
	"net/http"
)

var Log = config.Cfg().GetLogger()

func CreateRouter() http.Handler {
	router := mux.NewRouter()
	router.StrictSlash(true)

	// V1 Routes
	v1Router := router.PathPrefix("/v1").Subrouter()
	v1Router.HandleFunc("/", v1.API).Methods("GET")
	v1Router.HandleFunc("/gophers", httpmiddleware.Use(v1.GetGophers, middleware.RequireAPIKey)).Methods("GET")

	return httpmiddleware.Use(router.ServeHTTP, middleware.GetContext, httpmiddleware.RecoverInternalServerError)
}

```

### Update deps

Since we added new dependencies in this file, remember to run `dep ensure -v` again to update our deps.
