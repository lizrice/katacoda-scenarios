Start by writing a simple Go web server application. 

## Write your hello world web server

Write a basic web server in Go that will respond to a request on port 8080 with a simple message like "hello world".

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import "net/http"

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("hello world\n"))
	})

	http.ListenAndServe(":8080", nil)
}
</pre>

## Compile your app

`go build -o hello hello.go`{{execute}}

## Run your app

Run the app in the background (using the ampersand) so that you can still type into the terminal.

`./hello &`{{execute}}

## Does it work?

You should be able to see your app running at https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/



Start a new terminal window where you can make a request to your web server.

`curl https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/`{{execute}}
