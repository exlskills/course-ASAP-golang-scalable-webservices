### Using Go with Docker

To create a standard lightweight deploy for our project, we're going to use Docker. This is the `Dockerfile` that we'll create in the root directory of our project:

### Files to Setup

#### Dockerfile

This will build our Docker image. It goes into `Dockerfile` in our project root.

```Dockerfile
# Part 1 (Builder)
FROM golang:1.11 as gobuilder

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

#### .dockerignore

This is the file that Docker uses to know which directories to leave out of the build. It goes into `.dockerignore` in our project root.

```bash
# We don't want to have our local vendor pushed into the docker build
vendor/
```

### Convenience Scripts

For the following `.sh` files remember to make them executable with `chmod +x your-script.sh`

#### docker-build.sh

Build our docker image and tag it

```bash
echo 'Building docker image ...'
docker build -t demo-go-webservice .
```

#### docker-run.sh

Run our docker image locally in 'interactive' mode so that we can view the log output

```bash
echo "Running Docker image (don't forget about building!) ..."
docker run -it --rm -p 3333:3333 demo-go-webservice
```
