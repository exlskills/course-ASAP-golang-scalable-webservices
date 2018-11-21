### Using Go with Docker

To create a standard lightweight deploy for our project, we're going to use Docker. This is the `Dockerfile` that we'll create in the root directory of our project:

### Files to Setup

#### Dockerfile

This will build our Docker image. It goes into `Dockerfile` in our project root.

```bash
# Part 1 (Builder)
FROM golang:1.11-alpine3.7 as gobuilder

RUN apk add --no-cache curl git
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh

# TODO Replace `exlskills` with your GitHub username!
COPY . /go/src/github.com/exlskills/demo-go-webservice
WORKDIR /go/src/github.com/exlskills/demo-go-webservice

# Install dependencies
RUN dep ensure -v

# Build binary
RUN go build

# Part 2 (Final)
FROM alpine:3.7

# Copy over our binary from the builder step
COPY --from=gobuilder /go/src/github.com/exlskills/demo-go-webservice/demo-go-webservice /home/demo-go-webservice

# Configure entrypoint and expose our service's port (3333)
ENTRYPOINT /home/demo-go-webservice
EXPOSE 3333

```

#### Docker Ignore

This is the file that Docker uses to know which directories to leave out of the build. It goes into `.dockerignore` in our project root.

```bash
# We don't want to have our local vendor pushed into the docker build
vendor/
```

#### Docker Compose

In order to streamline our DB setup and connection -- as well as making mimicing a production setup easier, we will use docker compose to build/start/restart our service+DB. Update your `docker-compose.yml` file to be:

```yaml
version: '3.1'

services:
  web:
    build:
      context: .
    ports:
      - "3333:3333"
    environment:
      - GDEMO_DB_PATH=root:password@tcp(db:3306)/demogowsdb?charset=utf8mb4&parseTime=True
  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=demogowsdb
    volumes:
      - ./dockersql:/docker-entrypoint-initdb.d
```

### Docker Compose Commands (Future Reference)

NOTE: These won't work/do anything right now since our project isn't fully setup yet!

#### Build

`docker-compose build`

#### Run

`docker-compose -d up # -d option is for 'detatched' mode, remove to run it in the foreground`

#### Stop

`docker-compose down`

#### Follow Logs

`docker-compose logs -f # -f option is for 'follow' mode, to just get the last n lines, remove this option`
