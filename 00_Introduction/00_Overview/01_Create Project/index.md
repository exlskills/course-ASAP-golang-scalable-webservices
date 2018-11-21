### Start Hacking

These are the steps to start your new Go project. To follow Go convention, please replace `github.com/exlskills` with your GitHub path, i.e., `github.com/svarlamov` for me!

Make sure that your `GOPATH` environment variable is set, as this will use that for finding paths!

#### 1. Create Project Root

`mkdir -p $GOPATH/src/github.com/exlskills/demo-go-webservice`

All of the following steps will be from the `$GOPATH/src/github.com/exlskills/demo-go-webservice` dir, so make sure you `cd` into that if you haven't already!

#### 2. Setup Project Root

Setup Git repo (best practice): `git init`

Create a `.gitignore`:

```bash
echo '# Binaries for programs and plugins
*.exe
*.exe~
*.dll
*.so
*.dylib

# GoLand IDE files
.idea

# Dependencies (more on this later)
vendor/

# Ignore our binary
demo-go-webservice

# Test binary, build with `go test -c`
*.test

# Output of the go coverage tool, specifically when used with LiteIDE
*.out' > .gitignore

```

#### 3. Create entrypoint

```bash
echo 'package main

import (
    "fmt"
)

func main() {
    fmt.Println("Hello EXLskills!")
}

' > main.go

```

#### 4. Run fmt

Format our code and write in-place: `gofmt -w .`

#### 5. Build

`go build`

#### 6. Run

`./demo-go-webservice` (runs binary built from `go build`)

In the future, we will more frequently use `go run main.go` which will build on the fly and then run.

#### 7. Add dep

Since Go's out-of-the-box package manager won't let us share our requirements with implementers, we use `dep` (install it from [here](https://github.com/golang/dep) if you don't have it yet) to use the Go `vendor` directory standard and also give us a lockfile and package list to put into the Git repo. Some developers like to add the `vendor` directory to Git, however, for this project, we will opt to ignore it. Implementers will need to run `dep ensure` in order to get the dependencies or it will use their local versions.

Setup: `dep init`

Load: `dep ensure -v # The v flag will show us more detailed progress as it might take a bit with more dependencies`

#### 8. (Optional) Commit our first step

Add our changes: `git add .`

Commit our changes: `git commit -m"Init go project"`

Note: if you want to push to a remote, now would be the time to add it and start pushing!

### Project Source Code

For reference (or lazy typists!) you may find the full source on GitHub <a rel="noopener" target="_blank" href="https://github.com/exlskills/demo-go-webservice">here</a>.

### Intro Wrap Up

We now have a working minimal Go project that will form the basis of our web service and all related files. Now is a good time to open this up in an IDE as from here on out, there won't be such detailed explanations of steps like 'build', 'run', or 'create a go file'. It will be assumed that if you don't already know, that you can use this as a reference, or find a resource in the [EXLskills A$AP Go Open Course](https://exlskills.com/learn-en/courses/aap-learn-go-golang--learn_golang_asap)!
