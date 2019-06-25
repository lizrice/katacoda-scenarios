Now let's run your containerized app.

## Run a container from this image

The next command runs a container, based on the container image.

* The --rm automatically deletes the container when you stop it. It's optional.
* The -d option runs the container in the background (so that we can still access the terminal prompt)
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
