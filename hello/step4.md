Run the following command to see that your container is still running: 

`docker ps`{{execute}}

In the *PORTS* column you should see `0.0.0.0:18080->8080/tcp`. 

* `0.0.0.0` is equivalent to `localhost` and is an address for your local machine
* `18080` is the host port
* `8080` is the port number inside the container that the host port maps to

## Run a second container

Only one application can bind to a port at one time. The Go web server you have written is hard-coded to bind to port 8080. See what happens if you try to run more than one instance of the app. 

`./hello &`{{execute}}

`./hello`{{execute}}

You should see an error telling you that the address is already in use.

The second instance exited on error, but the first is still running, so let's kill it:

`kill %1`{{execute}}

When we run a container, we can have multiple instances running simultaneously by using different host ports. Try it by running another instance of the container, this time using host port 18081:

`docker run -d -p 18081:8080 hello`{{execute}}

`docker ps`{{execute}}

You should have two instances of the container running. You can make requests to either of them:

`curl localhost:18080`{{execute}}

`curl localhost:18081`{{execute}}

Inside their containers, both instances of the app are binding to port 8080, but because each container has its own network namespace, these are not the same port from the host machine's perspective.

You can run any number of instances of the same container on a host, provided they are all allocated unique port numbers.

## Have Docker dynamically pick a host port for you

You can specify just the container port number, and Docker will pick one of the available host ports for you. 

`docker run -d -p 8080 hello`{{execute}}

`docker ps`{{execute}}

The output from the `docker ps` command shows you the host port number that has been chosen. Can you find it? Add the host port number to the following curl command to check whether you've correctly identified it.

`curl localhost:`{{copy}}

## Next step

In this example you needed to know what port the containerized app is using, so that you can specify it on the `-p` parameter to the `docker run` command. In the next step you'll see how you can specify the container port number in the Dockerfile, so that this information is built into the container image and you don't need to know it at runtime.
