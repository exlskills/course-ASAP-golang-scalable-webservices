### Let's get this server going!

We now have **all** of the elements of our web server built, however, we don't have a server. So let's fix that and start seeing our service in action!

### Entrypoint

Our server's entrypoint is `main.go`, and that's where we will link up all of our service components:

```go
package main

import (
	"fmt"
	"github.com/exlskills/demo-go-webservice/config"
	"github.com/exlskills/demo-go-webservice/models"
	"github.com/exlskills/demo-go-webservice/routes"
	"github.com/gorilla/handlers"
	"net/http"
	"os"
	"time"
)

var Log = config.Cfg().GetLogger()
// This CORS Handler comes with some pretty lenient defaults, depending on your application, 
// you may want to curtail some of these open settings
var CORSHandler = handlers.CORS(handlers.AllowedMethods([]string{"GET", "POST", "PUT", "DELETE", "OPTIONS"}), handlers.AllowCredentials(), handlers.AllowedHeaders([]string{"x-locale", "x-api-key", "content-type", "access-control-request-headers", "access-control-request-method", "x-csrftoken"}), handlers.AllowedOrigins(config.Cfg().AllowedOrigins))

func main() {
	Log.Info("Setting up database connection ...")

	for {
		err := models.Setup()
		if err != nil {
			Log.WithError(err).Error("Error setting up database connection, retrying ...")
			time.Sleep(time.Second * 3)
		} else {
			break
		}
	}

	Log.Info("Connected to database")

	// TODO: Implement graceful stop
	Log.Info("Starting HTTP server")
	http.ListenAndServe(fmt.Sprintf("%s:%s", config.Cfg().ListenAddress, config.Cfg().ListenPort), CORSHandler(handlers.CombinedLoggingHandler(os.Stdout, routes.CreateRouter())))
	Log.Info("Stopped HTTP server")
}

```

### Update deps

Remember to run `dep ensure -v` make sure that all of deps are still in sync.

### Check our build

`go build`

### Build and run via Docker

`docker-compose up -d # Will build if you haven't already built`

### Running locally

The main constraint for running locally is setting up the MySQL DB on your box (or remote box). You will need to create a database, find/create a user for that database, run the `migrations/*.up.sql` and then the `seed/*.up.sql` scripts (in order). Once your DB is prepared, you will need to setup the connection string env var and run the service roughly like so: `GDEMO_DB_PATH="YourMySQLUser:YourMySQLPwd@tcp(localhost:3306)/TheNameOfYourDatabase?charset=utf8mb4&parseTime=True" go run main.go`. Running locally is not really meant to be in scope of this course and therefore isn't really supported, however, you should definitely try it and enjoy debugging the various errors that you may/not encounter!

### Project Source Code (Reference)

To compare your code, you may find the full reference source on GitHub <a rel="noopener" target="_blank" href="https://github.com/exlskills/demo-go-webservice">here</a>.
