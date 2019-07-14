In this scenario you will write a very simple Go application, build it into a container, and run it under Kubernetes. 

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "fmt"
  "os"
  "time"
)

func main() {
	for {
		fmt.Printf("hello, my hostname is %v\n", os.Hostname())
		time.Sleep(5*time.Second)
	}
}
</pre>

Build that code into a container with this Dockerfile:

<pre class="file" data-filename="Dockerfile" data-target="replace">
# First stage: start with a Golang base image
FROM golang:1.12-alpine3.10

# Move to the directory where the source code will live
WORKDIR /go/src/hello

# Copy the source code into the current directory
COPY . .

# Get any dependencies, and compile the code
RUN CGO_ENABLED=0 go get -v ./...

# Second stage: start from an empty base image
FROM scratch

# Copy the binary from the first stage
COPY --from=0 /go/bin/hello /

# Tell Docker what executable to run by default when starting this container
ENTRYPOINT ["/hello"]
</pre>

`docker build -t hello .`{{execute}}

You now have a container and in the next step you will run it using Kubernetes. But before we move on to that, let's describe what Kubernetes is.

## Container orchestration and more

If you have run scenarios like [Container basics](../hello)) you will have seen how you can run an individual container on a particular host by issuing `docker run` commands on that (virtual) machine. The basic function of a container orchestrator like Kubernetes is to run containers on your behalf. It chooses where to run each container across a number of nodes (known as a cluster).

In order to use containers effectively they may need to be connected together, and they may need to have access to the internet. Kubernetes can perform these functions too, along with managing permissions, secrets, connecting to storage, and many other capabilities that are needed to run a full application in the cloud.

## Reconciliation

Kubernetes uses declarative configuration - you tell it what state you want it to achieve, and it constantly checks whether the current state matches that config, making changes as necessary to bring reality in line with that desired state. This is called *reconciliation*.

## Kubernetes pods

One of the concepts you'll need to get to grips with when you start using Kubernetes is the *pod*. A pod consists of one or more containers that are always scheduled (run) together on a node. When you want to run a container, you define a pod specifying that container.

## Next step and further reading

In the next step you will define and run a Kubernetes pod to run your new image.

If you want to learn more about Kubernetes concepts before you move on, you can read [this article](https://medium.com/google-cloud/kubernetes-101-pods-nodes-containers-and-clusters-c1509e409e16) or dive into the [Kubernetes documentation](https://kubernetes.io/docs/). You can also find some great material introducing Kubernetes in Jerôme Petazzoni's [Container Training](https://qconuk2019.container.training/#76).
