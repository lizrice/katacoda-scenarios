Next we'll build your app into a container image, and run it as a container.

**In the terminal you should see a prompt asking for your username on Docker Hub.** This is going to be stored in an environment variable for use in later commands.

## Write a Dockerfile

<pre class="file" data-filename="Dockerfile" data-target="replace">
# Start with the scratch (empty) image
FROM scratch

# Copy the hello binary into the root directory
COPY hello hello

# Tell Docker what executable to run by default when starting this container
ENTRYPOINT ["/hello"]
</pre>

## Build a container image from this Dockerfile.

 The next command builds a container image, following the commands in the Dockerfile.

* We'll tag this image as `$yourname/hello`
* The -f specifies the Dockerfile to use
* The . means we're building the image in the context of the current directory

`docker build -t $yourname/hello  .`{{execute}}

Check that the container image exists.

`docker image ls`{{execute}}

You should see a container image called *$yourname/hello* in the output.

## Run a container from this image

The next command runs a container, based on that container image.

* The --rm automatically deletes the container when you stop it. It's optional.
* The -d option runs the container in the background
* The -p 8080:8080 tells Docker to map port 8080 in the container to port 18080 on the host

`docker run --rm -d -p 18080:8080 $yourname/hello`{{execute}}

## Look at the running containers

`docker ps`

This output should show a running container based on your image.

## Check you can access it

Note that we have mapped port 8080 inside the container to port 18080 on the host. The container port is only meaningful inside the container itself. We need to use the host port number to access the app:

`curl localhost:18080`{{execute}}

or:

`curl https://[[HOST_SUBDOMAIN]]-18080-[[KATACODA_HOST]].environments.katacoda.com/`{{execute}}

