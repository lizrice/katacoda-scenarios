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

*Feel free to modify the Go code to make this app your own! But if you change the port on which the server responds to requests (8080 in the example) you'll need to remember to change that as you follow the rest of the instructions in this scenario.*

## Next step 

We're not going to compile this code into a binary by running `go` manually on the command line. In the next step you'll see how to do the compilation step within a container image build.
