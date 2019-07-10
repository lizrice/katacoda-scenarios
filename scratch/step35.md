In this step you will add code to mount the container's `/proc` directory as a `proc` pseudo-filesystem. To do this without affecting mounts on the host, add a new namespace for mounts.

The flag `CLONE_NEWNS` tells the kernel to create a new mount namespace for the new process. Modify the code at line 36 to request this namespace as well as the UTS and PID namespaces.

<pre class="file" data-target="clipboard">
		Cloneflags:   syscall.CLONE_NEWUTS | syscall.CLONE_NEWPID | syscall.CLONE_NEWNS,
</pre>

In the `child()` function, add the code do the `proc` mount. This has to happen after the `chroot` call so that it refers to `/proc` from the container's perspective, so add this at line 67,

<pre class="file" data-target="clipboard">
    // Mount the proc pseudo-filesystem
    err = syscall.Mount("proc", "proc", "proc", 0, "")
    if err != nil {
        panic(fmt.Sprintf("mount: %v\n", err))
    }

</pre>

## Run ps inside a container

Run a shell now, and run `ps` inside the container. 

`go run main.go run sh`{{execute T1}}

`ps`{{execute T1}}

`ps -eaf`{{execute T1}}

You can see that the processes inside your container all have low numbers. 

## Container processes from the host's perspective

These containerized processes are still visible from the host. One way to show this is with a long-running process that we can look at from outside the process. Inside the container, sleep for, say, 30 seconds.

`sleep 30`{{execute T1}}

While the sleep is still running, we can find that process from the host's perspective:

`ps -C sleep`{{execute T2}}

* `ps` with the `-C` parameter looks for processes with the specified name

You should see that the process is visible from the host, but it has a high-numbered process ID. You can even find the process for your shell, which you already know has a low-numbered process ID from the container's perspective. 

`ps -C sh`{{execute T2}}





