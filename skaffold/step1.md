Start with a very simple app that writes a line to _stdout_ every few seconds. When this app is deployed in a Kubernetes pod, you will be able to see this output as the pod's log output. 

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "fmt"
  "time"
)

func main() {
	for {
		fmt.Println("hello")
		time.Sleep(5*time.Second)
	}
}
</pre>

## Multistage build Dockerfile

<pre class="file" data-filename="Dockerfile" data-target="replace">
# First stage: start with a Golang base image
FROM golang:1.12-alpine3.10

# Move to the directory where the source code will live
WORKDIR /go/src/hello

# Copy the source code into the current directory
COPY hello.go .

# Get any dependencies, and compile the code
RUN CGO_ENABLED=0 go get -v ./...

# Second stage: start from an empty base image
FROM scratch

# Copy the binary from the first stage
COPY --from=0 /go/bin/hello /

# Tell Docker what executable to run by default when starting this container
ENTRYPOINT ["/hello"]
</pre>

Build this image and check that it works. 

`docker build -t hello .`{{execute T1}}

`docker run -t hello`{{execute T1}}

This should log output to the screen on a regular basis. 

Stop this container from a second terminal.

`docker stop $(docker ps --filter ancestor=hello -q)`{{execute T2}}

* See the [Docker documentation](https://docs.docker.com/engine/reference/commandline/ps/#filtering) for more information about filtering the results from `docker ps`
* The `-q` parameter returns just the container ID, which can then be passed to the `docker stop` command.

## Next step

We have a very simple application that we can run in a container. Now let's get Skaffold to run it under Kubernetes. 