### Enough boilerplate! How do I actually respond to a request??

That's where controllers come in... But first a bit more boilerplate:

Setup our `controllers/v1/v1.go` file to get a package-wide logger:

```go
package v1

import "github.com/exlskills/demo-go-webservice/config"

var Log = config.Cfg().GetLogger()

```

And we'll also setup our first controller to just respond with success (useful for healthchecks, etc.). This will go into `controllers/v1/api.go`:

```go
package v1

import (
	"github.com/exlinc/golang-utils/jsonhttp"
	"net/http"
)

func API(w http.ResponseWriter, r *http.Request) {
	// TODO check service health, send some metrics, etc.
	jsonhttp.JSONSuccess(w, nil, "Server healthy")
}

```

### Controller that does something useful!

Yes, finally -- we're going to call our database, get our gophers (with pagination), and then respond with the result. This will go into our `controllers/v1/gophers.go` file:

```go
package v1

import (
	"github.com/exlinc/golang-utils/jsonhttp"
	"github.com/exlinc/golang-utils/queryparams"
	"github.com/exlskills/demo-go-webservice/models"
	"net/http"
)

func GetGophers(w http.ResponseWriter, r *http.Request) {
	limit, offset := queryparams.GetLimitOffsetQueryParametersDefaults(r)

	gophers, err := models.GetGophers(limit, offset)
	if err != nil {
		jsonhttp.JSONNotFoundError(w, "Error fetching gophers", "")
		return
	}

	jsonhttp.JSONSuccess(w, map[string]interface{}{"gophers": gophers}, "Successfully queried gophers")
}

```
