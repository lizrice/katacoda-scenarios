Take a look at the contents of the root directory in your "container".

`go run main.go run ls /`{{execute T1}}

Try running a shell:

`go run main.go run sh`{{execute T1}}

Explore what's accessible within the container. Here are a few examples you might like to try, but feel free to try your own ideas!

`ls /`{{execute T1}}

`pwd`{{execute T1}}

When you're done, don't forget to exit the program.

`exit`{{execute T1}}

You can't run the `bash` shell now, because the `bash` executable isn't present inside the alpine directory. This is exactly how a container is restricted to running only the executables that are included in its container image.

(That's not 100% true - if you mount additional directories into the container, you can also run executables from those mounted directories. But without mounts, the container can only access its copy of the container image contents.)

## Next steps

This is starting to feel like a container, right? By using `chroot` your process has access to a set of files and directories that are, to all intents and purposes, its own copy of Linux.

In a "proper" container, you can only see the processes running inside that container. In the next step we'll see how that's done, using the process ID namespace.
