In this step you'll write a Dockerfile that compiles your application and creates a container image.

## Write a Dockerfile based on Go

In order to compile your Go code you need Go installed. In this step we'll use a container base image that already includes Go, so that `go` is available *during the build step*. 

To understand this, you first have to appreciate that when you run `docker build` it creates a (temporary) container, in which it runs all the instructions defined in the Docker file. At first, there is nothing in that container's filesystem, but as it works through each instruction in the build, the filesystem gets modified. For example, when you have a `FROM` command this installs the base image into the temporary container.

<pre class="file" data-filename="Dockerfile" data-target="replace">
# Start with a Golang base image
FROM golang:1.12-alpine3.10

# Move to the directory where the source code will live
WORKDIR /go/src/hello

# Copy the source code into the current directory
COPY . .

# Get any dependencies, and compile the code
RUN CGO_ENABLED=0 go get -v ./...

# Tell Docker what executable to run by default when starting this container
ENTRYPOINT ["hello"]
</pre>

Build the image using this Dockerfile.

`docker build -t example  .`{{execute}}

Run a container from this image: 

`docker run -t example`{{execute}}

`docker run -t example I am a teapot`{{execute}}

* The image ENTRYPOINT defines which executable gets run when you run the container
* Any other arguments you pass in are also passed to that executable

>>Q1: What is the name of the executable that is built into the container image?<<
(*) hello
( ) example
( ) teapot

>>Q2: What is the repository name of the container image?<<
( ) hello
(*) example
( ) teapot

## Next step

