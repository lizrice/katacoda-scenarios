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
RUN /microscanner ${TOKEN} --no-verify

# Multi-stage build
# We will only get to this if the scanning succeeds
FROM ${IMAGE}
</pre>

Here in Katacoda let's use an environment variable to hold the name of the image you want to scan. Click the following command so you're prompted for the image name:

`read -p "Enter the image name: " image`{{execute}}

And now the image will be scanned when you run the following command

`docker build --build-arg TOKEN=$token --build-arg IMAGE=$image --no-cache .`{{execute}}

## A note on the `--no-verify` flag

In this example Dockerfile we specify the `--no-verify` flag when running `microscanner`. 

Running microscanner with the `--no-verify` flag means that you're not checking the certificates that identify the Aqua "CyberCenter" where the vulnerability database is held, when microscanner makes an HTTPS connection. This leaves you susceptible to the possibility that an attacker could spoof the CyberCenter address, and return inaccurate results for your scan. 

It is much better practice to omit this flag, but this requires the container image to have CA (certificate authority) certificates installed. Many images will have this already, but you may need to add steps to the Dockerfile to add them if they aren't there already. The command you need for this depends on the Linux distribution used in the base image (so unfortunately that means we can't use a single Dockerfile to scan _any_ image unless we specify `--no-verify`).

For example (Debian):

`RUN apt-get update && apt-get -y install ca-certificates`

or (Alpine):

`RUN apk add --no-cache ca-certificates && update-ca-certificates`

See the [Microscanner README](https://github.com/aquasecurity/microscanner/blob/master/README.md) for more information.

## Notes and further reading

* Luke Bond's [Microscanner wrapper](https://github.com/lukebond/microscanner-wrapper) for automatically creating Microscanner Dockerfiles
* More on [Docker multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
* Liz's talk about vulnerability scanning at [Cloud Native London](https://beta.skillsmatter.com/skillscasts/11727-what-s-so-hard-about-container-vulnerability-scanning) (requires sign-up)
