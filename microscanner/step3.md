Since you would typically scan each image as part of the CI/CD pipeline, there is no need for the MicroScanner executable to live in the image that you deploy. There are two ways you can ensure that you end up with an image that doesn't include the extraneous MicroScanner executable. 

* Add a step that removes microscanner from the image
* Multi-stage build

## An extra step in the Dockerfile

<pre class="file" data-filename="Dockerfile" data-target="append">

# Remove the microscanner exectuable
RUN rm /microscanner 
</pre>

Run the build and it should succeed. 

`docker build --build-arg TOKEN=$token --no-cache .`{{execute}}

## Multi-stage build

By having a second `FROM` command in the Dockerfile, we create a multi-stage build. In most multi-stage builds, some contents from the first stage are copied into the second stage. Here, however, we are relying on the fact that the first stage wil fail if the image contains high-severity vulnerabilties.

<pre class="file" data-filename="Dockerfile" data-target="replace">
# Start with the image we want to scan
FROM mongo:latest

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
FROM mongo:latest
</pre>

`docker build --build-arg TOKEN=$token --no-cache .`{{execute}}

Try this with both `mongo:3.2.1` and `mongo:latest` by editing the Dockerfile. Note that you have to change the image in two places in the Dockerfile!

## Notes and further reading

* Luke Bond's [Microscanner wrapper](https://github.com/lukebond/microscanner-wrapper) for automatically creating Microscanner Dockerfiles
* More on [Docker multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
* Liz's talk about vulnerability scanning at [Cloud Native London](https://beta.skillsmatter.com/skillscasts/11727-what-s-so-hard-about-container-vulnerability-scanning) (requires sign-up)
