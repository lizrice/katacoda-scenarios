Now you're going to modify the Dockerfile to specify the port number that the containerized app exposes. 

<pre class="file" data-filename="Dockerfile" data-target="append">
# The app uses port 8080
EXPOSE 8080
</pre>

# Build and run the container image

`docker build -t $yourname/hello .`{{execute}}
`docker run -d -P $yourname/hello`

* As before, the -d option runs the container in the background so we can continue using the terminal
* The -P option tells Docker to expose the port built into the container image

Docker dynamically allocates the host port. Find it as before:

`docker ps`{{execute}}

There are now several instances of your container running. They are displayed in reverse chronological order (newest first) so you will want the host port from the first entry in the list. Check that there is a response on this host port. 

`curl locahost:`{{copy}}

# Omit the -P option

You can also try running the container without the -P option, but in this case there is no exposed port so you won't be able to access it.

`docker run -d $yourame/hello`{{execute}}
`docker ps`{{execute}}

You will see an entry showing `8080/tcp` but this is just the container port. There is no mapping to a host port. (If you find you can get a response on port 8080, check that you aren't still running your hello application directly by running `ps`{{execute}}).
