Take a look at the contents of the root directory in your "container".

`go run main.go run ls /`{{execute T1}}

Try running a shell:

`go run main.go run sh`{{execute T1}}

Explore what's accessible within the container. Here are a few examples you might like to try, but feel free to try your own ideas!

`ls /`{{execute T1}}

`pwd`{{execute T1}}

When you're done, don't forget to exit the program.

`exit`{{execute T1}}

## Next steps

This is starting to feel like a container, right? By using `chroot` your process has access to a set of files and directories that are, to all intents and purposes, its own copy of Linux.

In a "proper" container, you can only see the processes running inside that container. In the next step we'll see how that's done, using the process ID namespace.
