In this step you'll build a container image that encapsulates your app.

## Write a Dockerfile

The Dockerfile describes the steps that Docker will take to build your container image.

<pre class="file" data-filename="Dockerfile" data-target="replace">
# Start with the scratch (empty) image
FROM scratch

# Copy the hello binary into the root directory
COPY hello /

# Tell Docker what executable to run by default when starting this container
CMD ["/hello"]
</pre>

## Build a container image from this Dockerfile.

 The next command builds a container image, following the commands in the Dockerfile.

`docker build -t hello  .`{{execute}}

* With the `-t` option we are giving this image the name `hello`. (See the notes below for more on image names and tags.)
* The `.` means we're building the image in the context of the current directory

Check that the container image exists.

`docker image ls`{{execute}}

You should see a few container images listed, including one called *hello*.

Your image has the tag *latest*. This is the default if you don't specify a tag. You could have explicitly specified this with `-t hello:latest` and the result would be identical.

## Next step

You have a container image! In the next step we'll run a container based on this image. Before we do that, if you'd like to learn more about how container images are identified, here are some optional extra reading and resources. 

### Notes and further reading

When you are pulling and pushing images from and to container registries (like Docker Hub) You can refer to your image in a few different ways:

* \[*registry*/\]*repository*/*name*:*tag*
* \[*registry*/\]*repository*/*name*@sha256:*image digest*

The *registry* is the host name of the container registry. If it's omitted, it's assumed you mean Docker Hub. The registry only becomes important when pushing and pulling images to and from a registry.

The *repository* is typically your username, or the name of your organization or project. This is another field that only really comes into play when you are using images stored in a registry. There are a set of "standard images" which have no repository name (for example `nginx` or `ubuntu`)

The *name* is the name of the image - in our case we have used `hello`.

The *image digest* is a hash of the image contents, and it's an immutable identifier for that exact version of the image. If you rebuild the image, it will have a different hash.

The *image digest* and the *tag* are both ways of identifying versions of the same image. The digest is unique to the particular image, but you can give any image any tag you like. The same tag might be re-used to refer to different versions of the image. In addition, a given image can have any number of different tags. 

In practice, tags are often used to hold the semantic version numbers. For example, if the most recent version of a particular component is version 2.1.3, the same image might have tags "2", "2.1", "2.1.3" and "latest". If someone pulls a version without specifying the tag, they will get this version because the tag defaults to "latest".

Read more about image IDs and tags:

* [Explaining Docker Image IDs](https://windsock.io/explaining-docker-image-ids/)
* [Docker Tag vs Hash: A Lesson in Deterministic Ops](https://medium.com/@tariq.m.islam/container-deployments-a-lesson-in-deterministic-ops-a4a467b14a03)
* [Using Docker tags to mess with people's minds](https://medium.com/microscaling-systems/using-docker-tags-to-mess-with-peoples-minds-367bb2c93bd0)
