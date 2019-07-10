
You can change the hostname in go with a line like this: 

`syscall.Sethostname([]byte("container"))`

But where could this go in the current program if you want to change the hostname just for the containerized process? 

* If you add this line before the call to `cmd.Run()` then the new UTS namespace hasn't been created yet. 
* If you add it after `cmd.Run()`, it wouldn't run until after the containerized process completes. When that process terminates, any new namespaces that were created for it will also be destroyed.

So in either case, the effect would be to set the hostname in the host's UTS namespace - and this would change the hostname for the host, not the cotnainer. 

## Cloning another process

The trick to making this work is to create another process. 

* Clone one process with the new namespace
* Change the hostname within this process
* Then clone another process from this child to run the arbitrary command

To make your program do this, we can take advantage of the fact that it can run any command - including running itself. At the moment it expects `run` as the first argument, but we will make it handle another option, `child`. 

Add another option into the switch statement; insert the following code at line 15:

<pre class="file" data-target="clipboard">
        case "child":
            child()
</pre>

Add the `child()` function. At the moment, this is just like `run()` except that it doesn't need to create a new namespace. 

<pre class="file" data-filename="main.go" data-target="append">

func child() {
    // Arguments 2 onwards are the arbitrary command we're going to run
    fmt.Printf("Running %v\n", os.Args[2:])

    // Set up a struct that describes the command we want to run
    cmd := exec.Command(os.Args[2], os.Args[3:]...)
    cmd.Stdin = os.Stdin
    cmd.Stdout = os.Stdout
    cmd.Stderr = os.Stderr
    
    // This is where we run the command
    err := cmd.Run()
    if err != nil {
      panic(fmt.Sprintf("running: %v\n", err))
    }
}
</pre>

Modify the `run()` function so that instead of running the arbitrary command, it calls your program again, passing all the same arguments except replacing `run` with `child`. To do this, replace line 29 with these lines:

<pre class="file" data-target="clipboard">
    args := os.Args
    args[1] = "child"
    cmd := exec.Command("/proc/self/exe", args[1:]...)
</pre>

`"/proc/self/exe"` is always a link to the currently running executable. To test this out, try the following command:

`ls -l /proc/self/exe`{{execute T1}}

Try this version of the program:

`go run main.go run echo hello`{{execute T1}}

You should see the output from both `run` and `child` invocations of the process. Do you understand why the output appears in the order you see it?

## Change the hostname

Now we have an opportunity to change the hostname within the child process, which has the new UTS namespace, before executing the arbitrary command. Insert the following lines at line 50:

<pre class="file" data-target="clipboard">
    // Set the hostname for the container
    err := syscall.Sethostname([]byte("container"))
    if err != nil {
      panic(fmt.Sprintf("sethostname: %v\n", err))
    }

</pre>

You will also need to change `:=` to `=` at line 62 because the `err` variable is now being used earlier in this function.

Now, if you run a shell in your container you should find that the hostname has been set up for your container:

`go run main.go run bash`{{execute T1}}

`hostname`{{execute T1}}

Check that the host hasn't been affected (the next command will run in Terminal 2):

`hostname`{{execute T2}}

## Next step

Now you have a program that can run an arbitrary command, inside a process that has its own hostname isolated from the host. In the next step you'll give this process its own limited view of the filesystem, using `chroot`.

Don't forget to quit out of the currently running shell before you move on.

`exit`{{execute T1}}
