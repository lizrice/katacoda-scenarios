We're going to create a program that can run an arbitrary command in a containerized process. This will be an illustration of what happens when you do a `docker run` command.

_This isn't going to be production-quality code!_

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
    err = cmd.Run()
    if err != nil {
      panic(fmt.Sprintf("running: %v\n", err))
    }
}
</pre>

Build and run this command:

`go run main.go run echo hello`{{execute}}

* `go run main.go` builds and runs the program
* `run` is argument 1 - you'll see why we want this in a later step
* `echo hello` is the command we want to run

At this stage there is no containerization - your program runs the command in a regular Linux process. The command `echo hello` gets executed, then the program completes.

Note that you can also run a shell as your arbitrary command:

`go run main.go run bash`{{execute T1}}

You'll see a command prompt, but notice that the `== Finished ==` message doesn't get output straight way. The program is blocked while the shell process in running. You'll only see the `== Finished ==` message when you exit that shell:

`exit`{{execute T1}}

## Use `ps` to check what is running

You can use a second terminal window to confirm what's running. First check what processes are running under your user ID:

`ps -af`{{execute T2}}

If nothing else is running, you should only see output for the `ps` command you were just running. 

Run your program again (this will run in the first terminal window):

`go run main.go run bash`{{execute T1}}

While your program is still running, check the processes again:

`ps -af`{{execute T2}}

You should see four processes running:

* `go run main.go run bash` is the command you executed to compile and then run your program
* go compiled and wrote the executable to the /tmp directory. You should be able to see that executable in the process list
* Your program cloned a new process to run `bash`
* You should also see the process for `ps` that you just ran

Finally, quit out of your program:

`exit`{{execute T1}}

Want to check what's still running?

`ps -af`{{execute T2}}

# Next step

In the next step you will start to containerize the process by giving it its own namespace for its host name. 