We're going to create a program that can run an arbitrary command in a containerized process. This will be an illustration of what happens when you do a `docker run` command.

First let's start by writing some code that will execute an arbitrary command. 

<pre class="file" data-filename="main.go" data-target="replace">
package main

import (
  "fmt"
  "os"
  "os/exec"
)

func main() {
	// We expect "run" as the first argument
	switch os.Args[1] {
		case "run":
			run()
		default:
			panic("Bad argument")
	}

	fmt.Println("== Finished ==")
}

func run() {
	// Arguments 2 onwards are the arbitrary command we're going to run
	fmt.Printf("Running %v\n", os.Args[2:])

	// Set up a struct that describes the command we want to run
	cmd := exec.Command(os.Args[2], os.Args[3:]...)
	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	// This is where we run the command
	cmd.Run()
}
</pre>

Build and run this command:

`go run main.go run echo hello`{{execute}}

* `go run main.go` builds and runs the program
* `run` is argument 1 - you'll see why we want this in a later step
* `echo hello` is the command we want to run

At this stage there is no containerization - your program runs the command in a regular Linux process. 

Note that you can also run a shell as your arbitrary command:

`go run main.go run bash`{{execute}}

You'll see a command prompt, but notice that the `== Finished ==` message doesn't get output straight way. You'll only see that when you exit this shell: 

`exit`{{execute}}

# Next step

In the next step you will start to containerize the process by giving it its own namespace for its host name. 