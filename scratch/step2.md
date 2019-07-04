Now you'll start creating something that behaves a bit like a container! 

Copy the following and insert it at line 30 in main.go. 

<pre class="file" data-target="clipboard">

	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags:   syscall.CLONE_NEWUTS,
    }
</pre>

This adds some additional information describing the command we want to execute. 

* `CLONE_NEWUTS` indicates that we want a new namespace for the "Unix Timesharing System". While this sounds complicated, for our purposes this just concerns the host name
* `Cloneflags` indicate flags for when the new process is "cloned" (created) 

You will also need to add `"syscall"` to the list of imports at lines 3-7.

Before you run this, check the hostname:

`hostname`{{execute T1}}

You should see `host01`.

Ask this program to run a shell:

`go run main.go run bash`{{execute T1}}

Now try changing the hostname:

`hostname something`{{execute T1}}

Check the hostname within this shell:

`hostname`{{execute T1}}

You should see that this has changed. But this has only taken effect without your "container". There is a second terminal window onto the VM. If you run `hostname` directly on the VM you should see the original hostname hasn't changed.

`hostname`{{execute T2}}

## Containerization

This illustrates one of the key aspects of containerization. The shell process has its own namespace for the hostname, which means that you can modify it without affecting the hostname for the rest of the VM. From the shell process's perspective, it can only see its own hostname in its own namespace. 

You can also change the hostname on the VM without affecting the namespace that you'll see in the shell process. Try it!

`hostname vm-host`{{execute T2}}

`hostname`{{execute T2}}

`hostname`{{execute T1}}

## Next step

In the next step you'll give the shell process its own namespace for process IDs.
