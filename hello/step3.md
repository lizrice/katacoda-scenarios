Now it's time to run your app inside a container. 

The next command runs a container, based on the container image you just built.

`docker run --rm -d -p 18080:8080 $yourname/hello`{{execute}}

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