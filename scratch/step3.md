The aim here is that when you run `ps` inside a container, you should only see processes running inside that container, not on the host. 

There are several different types of namespace, and earlier in this scenario we used the UTS namespace to give the process a hostname that is independent of the host's own hostname. Another type of namespace is the process ID (PID) namespace.

## Create a PID namespace

Edit the code that's now at around lines 35-37 so that it requests a new PID namespace as well as a new UTS namespace:

<pre class="file" data-target="clipboard">
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags:   syscall.CLONE_NEWUTS | syscall.CLONE_NEWPID,
    }
</pre>

At the moment the code prints out what commands it's going to execute. Edit lines 26 and 48 to also shows the current process ID:

<pre class="file" data-target="clipboard">
	fmt.Printf("Running %v, process ID %d\n", os.Args[2:], os.Getpid())
</pre>


Ask this program to run a shell:

`go run main.go run sh`{{execute T1}}

* The `run()` function has a high-numbered process ID that has been allocated from the host's process namespace.
* The `child()` function has process ID 1, because it is the first process in the new process namespace.

## Running ps

What do you expect to see when you run `ps` in this shell?

`ps -eaf`{{execute T1}}

At this point you should see no entries. Why doesn't this show at least an entry for the `ps` command you were just running? This is because of the way `ps` works.

## What does `ps` do

The `ps` program finds out about running programs by looking in the `/proc` directory, which holds information about all the running processes. Take a look at `/proc` on the host:

`ls /proc`{{execute T2}}

This directory is mounted as a pseudo-filesystem so that the kernel knows that it should write process information here. You can see this by looking at the mounts: 

`mount | grep proc`{{execute T2}}

You should see a mount of type `proc` at `/proc`.

However, inside your "container" there is nothing inside the `/proc` directory:

`ls /proc`{{execute T1}}

If you try to run `mount` inside your "container" you will see an error:

`mount`{{execute T1}}

In the next step you will add the mount point for proc, but first, exit the program.

`exit`{{execute T1}}

## Next step

In the next step we will add a mount point for the proc pseudo-filesystem, so that you can run `ps` effectively inside your container.



