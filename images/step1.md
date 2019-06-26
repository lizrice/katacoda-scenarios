Start by writing a simple Go web server application. This is very similar to the app in the *Container basics* scenario, but this time the response content is read from a file rather than being hard-coded. 

## Hello world

Write a basic web server in Go that will respond to a request on port 8080 with a simple message like "hello world". The editor pane on the top right of this screen is already set up to edit a file called `hello.go`. You can click the button below to copy the code into that file.

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "os"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		response, err := ioutil.ReadFile("./text/response")
		if err != nil {
			fmt.Printf("reading file: %v\n", err)
			os.Exit(1)
		}

		fmt.Printf("response: %s\n", response)
		w.Write(response)
	})

	err := http.ListenAndServe(":8080", nil)
	if err != nil {
	  fmt.Printf("serving: %v\n", err)
	  os.Exit(1)
	}
}
</pre>

Compile this app:

`CGO_ENABLED=0 go build -o hello hello.go`{{execute}}

You'll need to define the response file that this application code reads from. 

<pre class="file" data-filename="text/response" data-target="replace">
Here's a response!
</pre>


## Run your app

Run the app in the background (using the ampersand) so that you can still type into the terminal.

`./hello &`{{execute}}

Check you get the response you expect when making a request to your application using `curl`:

`curl localhost:8080`{{execute}}

You should see the text message returned to you.

## Stop the application

When you're satisfied that this application works, let's stop it:

`kill %1`{{execute}}

## Next step

At this stage you have a compiled Go binary and a text file that it reads from. In the next step we'll build a container image that includes both the binary and the text file.
