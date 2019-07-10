When you run a container, it has access to files and directories that come from its container image. We need to give our container is own set of files and directories. For illustration let's use Alpine Linux.

## Download Alpine Linux

Let's download a copy of Alpine Linux to use for the "container image". Rather than using a real container image, you're going to download a tar file of the files and directories, and uncompress them into a normal directory on the file system. 

`mkdir alpine`{{execute T2}}

`cd alpine`{{execute T2}}

`curl -o alpine.tar.gz http://dl-cdn.alpinelinux.org/alpine/v3.10/releases/x86_64/alpine-minirootfs-3.10.0-x86_64.tar.gz`{{execute T2}}

`tar xvf alpine.tar.gz`{{execute T2}}

Remove the compressed version:

`rm alpine.tar.gz`{{execute T2}}

Move back to the home directory and then check that the contents of the `alpine` directory look like a distribution of Alpine, with `bin`, `lib`, `var`, `tmp` and so on.

`cd ..`{{execute T2}}

`ls alpine`{{execute T2}}

Now you can use this `alpine` directory as the root for your container. 

## Changing the root

You change the root for the current process with `syscall.Chroot()`. This leaves you in an undefined location in the new directory hierarchy! So you also need to explicitly change directory with `syscall.Chdir()`.

Copy the following lines and insert them into the `run()` function at line 26.

<pre class="file" data-target="clipboard">

    // Change the root directory
    err := syscall.Chroot("/root/alpine")
    if err != nil {
      panic(fmt.Sprintf("chroot: %v\n", err))
    }

    // Move into the new root directory
    err = syscall.Chdir("/")
    if err != nil {
      panic(fmt.Sprintf("chdir: %v\n", err))
    }

</pre>

You will also have to change `err := cmd.Run()` to `err = cmd.Run()` (at around line 48) since we're now using that err variable earlier in the same function.

## Notes on executing a command in the new directory

Before running the program it's worth thinking about what is going to happen:

* Your program will move to running in a root directory that corresponds to `/root/alpine`
* It won't have access to any files outside of its new root directory
* If you want your program to run an executable, that executable file needs to be accessible - so it needs to be within the new root directory

In other words, your program will now only be able to run executables that exist within the `alpine` directory.

You've done nothing to change the `PATH` environment variable. Take a look at it:

`echo $PATH`{{execute T1}}

When you ask your arbitrary program to run a command, it will try to find in on the `PATH`, but in the context of the new root directory.

Compare what's in `/bin` on the machine with what's in `/bin` inside `alpine`:

`ls /bin`{{execute T1}}

`ls /root/alpine/bin`{{execute T1}}

>>Q1: Which of the following commands do you think you'll be able to run inside your container program?<<
[*] echo
[ ] bash
[*] sh
[*] ls
