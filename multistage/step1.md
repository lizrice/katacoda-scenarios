Start by writing a simple Go web server application. 

## Hello world

Write a basic web server in Go that will respond to a request on port 8080 with a simple message like "hello world". The editor pane on the top right of this screen is already set up to edit a file called `hello.go`. You can click the button below to copy the code into that file. 

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "fmt"
  "os"
)

func main() {
	fmt.Printf("Here are my arguments: %v\n", os.Args[:])
}
</pre>

## Next step

We're not going to compile this code into a binary by running `go` manually on the command line. In the next step you'll see how to do the compilation step within a container image build.
