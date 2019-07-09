The aim here is that when you run `ps` inside a container, you should only see processes running inside that container, not on the host. 

There are several different types of namespace, and in the previous step we used the UTS namespace to give the process a hostname that is independent of the host's own hostname. Another type of namespace is the process ID (PID) namespace. 

## Create a PID namespace

Edit the code that's now at lines 31-33 so that it requests a new PID namespace as well as a new UTS namespace:

<pre class="file" data-target="clipboard">

	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags:   syscall.CLONE_NEWUTS | syscall.CLONE_NEWPID,
    }
</pre>

At the moment the code prints a line to say what commands it's going to execute. Edit that line so that it also shows the current process ID: 

<pre class="file" data-target="clipboard">
	fmt.Printf("Running %v, process ID %d\n", os.Args[2:], os.Getpid())
</pre>


Ask this program to run a shell:

`go run main.go run bash`{{execute T1}}

What do you expect to see when you run `ps` in this shell? 

`ps -eaf`{{execute T1}}

This shows all the processes on the host! So you might have the impression that nothing has been achieved with that process ID namespace. 

Stop this program:
`exit`{{execute T1}}

## What does `ps` do

The `ps` program finds out about running programs by looking in the `/proc` directory, which holds information about all the running processes. Take a look inside it.

`ls /proc`{{execute T1}}

This directory is mounted as a pseudo-filesystem so that the kernel knows that it should write process information here. You can see this by looking at the mounts: 

`mount | grep proc`{{execute T1}}

You're going to need a different proc directory for the container.

## Download Alpine Linux

Let's download a copy of Alpine Linux that we will use for the container image.

`mkdir alpine`{{execute T2}}

`cd alpine`{{execute T2}}

`curl -o alpine.tar.gz http://dl-cdn.alpinelinux.org/alpine/v3.10/releases/x86_64/alpine-minirootfs-3.10.0-x86_64.tar.gz`{{execute T2}}

`tar xvf alpine.tar.gz`{{execute T2}}

Now we can use this as the root 



