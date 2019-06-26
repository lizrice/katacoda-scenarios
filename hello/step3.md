Now it's time to run your app inside a container. 

The next command runs a container, based on the container image you just built.

`docker run --rm -d -p 18080:8080 hello`{{execute}}

* The -d option runs the container in the background (so that we can still access the terminal prompt)
* The -p 18080:8080 tells Docker to map port 8080 in the container to port 18080 on the host

You'll see a long string of hex digits printed to the terminal. That's the unique ID for that instance of the running container.

## Look at the running containers

`docker ps`{{execute}}

This output should show a running container based on your image.

## Check you can access it

Note that we have mapped port 8080 inside the container to port 18080 on the host. Recall that your Go app is serving from port 8080. The container port is only meaningful inside the container itself, and we need to use the **host** port number to access the app:

`curl localhost:18080`{{execute}}

or:

`curl https://[[HOST_SUBDOMAIN]]-18080-[[KATACODA_HOST]].environments.katacoda.com/`{{execute}}

## Next step

You specified that the host port would be 18080 with the -p parameter; you also specified that the port to access inside the container is 8080. In the next step we'll see some other options for specifying the host and container port values.

# Notes and further reading

In much the same way as in git, you don't have to use the full string of digits to identify a container, you just have to supply enough characters to uniquely identify the container. This is true for container image IDs as well.

In the `docker ps` output you'll also see a randomly-generated, human-readable name for the container. You can give a container a particular name by specifying it on the `docker run` command with the `--name` parameter. Find out more in [this article](https://www.digitalocean.com/community/tutorials/naming-docker-containers-3-tips-for-beginners). 
