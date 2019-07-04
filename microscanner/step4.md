We can use a single Dockerfile to scan any image.

<pre class="file" data-filename="Dockerfile" data-target="replace">
# The image name will be passed in as a build argument
ARG IMAGE

# Start with the image we want to scan
FROM ${IMAGE}

# Add the microscanner binary
ADD https://get.aquasec.com/microscanner /

# Make it executable
RUN chmod +x /microscanner

# Indicate that the token will be passed in as a build argument
ARG TOKEN

# Run microscanner to scan the image contents
RUN /microscanner ${TOKEN}

# Multi-stage build
# We will only get to this if the scanning succeeds
FROM ${IMAGE}
</pre>

Here in Katacoda let's use an environment variable to hold the name of the image you want to scan. Click the following command so you're prompted for the image name:

`read -p "Enter the image name: " image`{{execute}}

And now the image will be scanned when you run the following command

`docker build --build-arg TOKEN=$token --build-arg IMAGE=$image --no-cache .`{{execute}}

## Notes and further reading

* Luke Bond's [Microscanner wrapper](https://github.com/lukebond/microscanner-wrapper) for automatically creating Microscanner Dockerfiles
* More on [Docker multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
* Liz's talk about vulnerability scanning at [Cloud Native London](https://beta.skillsmatter.com/skillscasts/11727-what-s-so-hard-about-container-vulnerability-scanning) (requires sign-up)
