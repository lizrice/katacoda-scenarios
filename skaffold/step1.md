Start by writing a simple Go web server application. 

## Hello world

Write a basic web server in Go that will respond to a request on port 8080 with a simple message like "hello world". The editor pane on the top right of this screen is already set up to edit a file called `hello.go`. You can click the button below to copy the code into that file.

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "net/http"
  "fmt"
  "os"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("hello world\n"))
	})

	err := http.ListenAndServe(":8080", nil)
	if err != nil {
	  fmt.Printf("serving: %v\n", err)
	  os.Exit(1)
	}
}
</pre>

## Compile your app

Clicking on commands like the line below, will copy and execute it in the terminal window on the bottom right of this screen.

`CGO_ENABLED=0 go build -o hello hello.go`{{execute}}

We are turning off "CGO_ENABLED" in order to build a standalone binary executable file that we'll be able to use inside a container image, without any other dependencies required.

## Run your app

Run the app in the background (using the ampersand) so that you can still type into the terminal.

`./hello &`{{execute}}

## Check it works

You can make a request to your application using `curl`:

`curl localhost:8080`{{execute}}

You should see the text message returned to you.

### Host names in this Katacoda environment

We've just made the `curl` request to the active port (8080, if you didn't change it) on `localhost`. On Katacoda you can also reach active ports through a dynamically-configured host name for your environment. In this case you can access your app through your browser by visiting https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

You can also make a request using `curl`:

`curl https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/`{{execute}}

You should also be able to issue that curl request from a terminal on your laptop, because that address is exposed to the internet.

## Stop the application

When you're satisfied that this application works, let's stop it:

`kill %1`{{execute}}

Verify that *hello* is no longer running:

`ps`{{execute}}

## Next step

At this stage you have a compiled Go binary. In the next step we'll build a container image that includes that binary.
