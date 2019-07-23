In this step you'll pass a message to your application as an environment variable.

Modify the Go program to read an environment variable and use that for the web response:

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "fmt"
  "net/http"
  "os"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		response := os.Getenv("GO_APP_RESPONSE")
		fmt.Printf("response: %s\n", response)
		w.Write([]byte(response))
	})

	err := http.ListenAndServe(":8080", nil)
	if err != nil {
	  fmt.Printf("serving: %v\n", err)
	  os.Exit(1)
	}
}
</pre>

Build this version of the application:

`CGO_ENABLED=0 go build -o hello hello.go`{{execute}}

Build the container image with this version of the application:

`docker build -t hello  .`{{execute}}

## Pass environment var at runtime

You can pass the environment variable as part of the Docker command to run the container:

`docker run -d --rm -e GO_APP_RESPONSE="Response from an environment variable" -p 18082:8080 hello`{{execute}}

This time you can access the container on port 18082.

`curl localhost:18082`{{execute}}

## Build environment variable into the image

If the environment variable isn't going to change, you can add it into the container image at build time by adding an `ENV` command into the Dockerfile.

<pre class="file" data-filename="Dockerfile" data-target="append">
# Define an environment variable
ENV GO_APP_RESPONSE="Another environment variable response"
</pre>

You'll need to rebuild the image with this instruction:

`docker build -t hello  .`{{execute}}

This time there is no need to pass in the environment variable:

`docker run -d --rm -p 18083:8080 hello`{{execute}}

Check the response you get from port 18083:

`curl localhost:18083`{{execute}}

## Overriding built-in environment variables

You have just seen that the environment variable specified at container image build time is being accessed by the Go application. You could also try over-riding that at runtime.

`docker run -d --rm -e GO_APP_RESPONSE="You can change an env var at runtime" -p 18084:8080 hello`{{execute}}

Check your answer!

`curl localhost:18084`{{execute}}

## Notes and extra reading

You can see any environment variables built into the image with `docker inspect`.

`docker inspect -f "{{.Config.Env}}" hello`{{execute}}

Anyone who can run this command on your image can see any environment variables you specified. It's not a safe place to store anything secret.

For that reason, **don't hard-code sensitive data like passwords into your images!** 

In Kubernetes and in Docker, there are dedicated [secret objects](https://kubernetes.io/docs/concepts/configuration/secret/) for handling credentials that your application code might need.
