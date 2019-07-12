Let's see how big this image is. The next command shows the size of the image in bytes

`docker inspect example --format='{{.Size}}'`{{execute}}

This gives the size in bytes. You should see a result equivalent to around 350MB.

We can also see the size of the Go image that was used as the base image.

`docker inspect golang:1.12-alpine3.10 --format {{.Size}}`{{execute}}

You can see that the Go image is nearly as big as the resulting example image.

As you'll have seen if you worked through the [Container basics: containers and images](../hello) scenario, when you're creating an image for a standalone Go binary, you can build from the _scratch_ or empty base image. We have started from the _golang_ image so that we have the Golang compiler available, but we only need that to build the binary, not for running it.

We can create a much smaller image by using a Multi-stage Build.

## Multi-stage Build

A Dockerfile for a multi-stage build simply has more than one FROM command. Each new FROM command starts a new stage, and you can copy files from an earlier stage.

For a Go application it's common to have a two-stage image build process:

* The first stage is FROM a golang base image and is used to compile the code
* The second stage is FROM a scratch (empty) image

Try it out with this Dockerfile:

<pre class="file" data-filename="Dockerfile" data-target="replace">
# First stage: start with a Golang base image
FROM golang:1.12-alpine3.10

# Move to the directory where the source code will live
WORKDIR /go/src/hello

# Copy the source code into the current directory
COPY . .

# Get any dependencies, and compile the code
RUN CGO_ENABLED=0 go get -v ./...

# Second stage: start from an empty base image
FROM scratch

# Copy the binary from the first stage
COPY --from=0 /go/bin/hello /

# Tell Docker what executable to run by default when starting this container
ENTRYPOINT ["/hello"]
</pre>

Build and run an image built with this version of the Dockerfile:

`docker build -t example .`{{execute}}

`docker run -t example`{{execute}}

If you look at the size of this image, it's much smaller:

`docker inspect example --format {{.Size}}`{{execute}}

You should see it's around 2MB now.

## Notes and further reading

Why does the size of an image matter?

* It takes a less time to transfer a smaller image across a network.
* The less software is inside a container, the smaller the attack surface.

## Extra exercises and further reading

You can find out lots of information about a container image with `docker image inspect`. See if you can related the Dockerfile commands like `ENTRYPOINT` and `WORKDIR` to this output. 

`docker image inspect example`{{execute}}

You can also find out about how the image was created with the `docker image history` command:

`docker image history example`{{execute}}

* Read more about how [image layers affect the final image size](https://developers.redhat.com/blog/2016/03/09/more-about-docker-images-size/)
* Read about how [performance and the number of vulnerabilities can be affected by image size](https://cloud.google.com/blog/products/gcp/kubernetes-best-practices-how-and-why-to-build-small-container-images)

