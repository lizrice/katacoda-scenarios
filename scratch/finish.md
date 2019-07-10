Congratulations, you built your own container!

You now know that a container is really just a Linux process that has a restricted view.

* **Namespaces** limit what the container process can see - for example, giving the container an isolated set of process IDs. 
* **Changing the root** limits the set of files and directories that the container can see.

There is a third construct that limits the resources that a container has access to: control groups or **cgroups**. There are different types of cgroup for different types of resources: memory, CPU, process, I/O and more. The cgroup controls how much of that resource is available to its members. A process is assigned to a cgroup so that it can only have access to that limited resource.

## Notes and further reading

* Find a version of this code at [lizrice/containers-from-scratch](https://github.com/lizrice/containers-from-scratch) on GitHub
* See cgroups in action in [this video](https://youtu.be/MHv6cWjvQjM?t=1322)
