Next we'll build a container image that encapsulates your app.

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

You should see a few container images listed, including one called *$yourname/hello*.

Your image has the **tag** *latest*. This is the default if you don't specify a tag.

# Notes and further reading

When you are pulling and pushing images from and to container registries (like Docker Hub) You can refer to your image in a few different ways:

* *repository*/*image*:*tag*
* *repository*/*image*@sha256:*image digest*

The *image digest* is a hash of the image contents, and it's an immutable identifier for that exact version of the image. If you rebuild the image, it will have a different hash.

In contrast, you can give any image any tag you like. The same tag might be re-used to refer to different versions of the image.

Read more about image IDs and tags:

* [Explaining Docker Image IDs](https://windsock.io/explaining-docker-image-ids/)
* [Docker Tag vs Hash: A Lesson in Deterministic Ops](https://medium.com/@tariq.m.islam/container-deployments-a-lesson-in-deterministic-ops-a4a467b14a03)
* [Using Docker tags to mess with people's minds](https://medium.com/microscaling-systems/using-docker-tags-to-mess-with-peoples-minds-367bb2c93bd0)


