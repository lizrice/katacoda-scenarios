When you run a container, it has access to files and directories that come from its container image. We need to give our container is own set of files and directories. For illustration let's use Alpine Linux.

## Download Alpine Linux

Let's download a copy of Alpine Linux to use for the "container image". Rather than using a real container image, you're going to download a tar file of the files and directories, and uncompress them into a normal directory on the file system. 

`mkdir alpine`{{execute T2}}

`cd alpine`{{execute T2}}

`curl -o alpine.tar.gz http://dl-cdn.alpinelinux.org/alpine/v3.10/releases/x86_64/alpine-minirootfs-3.10.0-x86_64.tar.gz`{{execute T2}}

`tar xvf alpine.tar.gz`{{execute T2}}

Remove the compressed version:

`rm alpine.tar.gz`{{execute T2}}

Move back to the home directory and then check that the contents of the `alpine` directory look like a distribution of Alpine, with `bin`, `lib`, `var` and so on.

Now you can use this `alpine` directory as the root for your container. 

## Changing the root

You change the root for the current process with `syscall.Chroot()`. 





