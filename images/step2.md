In this step you'll build a container image that encapsulates your app along with the text file it needs in order to run.

## Write a Dockerfile

The Dockerfile describes the steps that Docker will take to build your container image.

<pre class="file" data-filename="Dockerfile" data-target="replace">
# Start with the scratch (empty) image
FROM scratch

# Copy the hello binary into the root directory
COPY hello /

# Copy the response text file into the location where the container expects it to be
COPY text/response /text/response

# Tell Docker what executable to run by default when starting this container
CMD ["/hello"]
</pre>

## Build a container image from this Dockerfile

The next command builds a container image, following the commands in the Dockerfile.

`docker build -t hello  .`{{execute}}

You have built a container image that includes both the executable and the text file it needs in order to run. This is one of the big advantages of containers - a container image can act as a package for everything that an application needs in order to run. If you pass the container image to someone else, it has everything they need and the container should run just the same on their system as it does on yours.

Now let's move on to running this container.
