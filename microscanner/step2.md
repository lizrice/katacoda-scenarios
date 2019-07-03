As an example, we'll scan an old version of the `mongo` image which is known to have several vulnerabilities. 

## Scanning an image with vulnerabilties

Copy this Dockerfile into the editor (the file will save automatically).

<pre class="file" data-filename="Dockerfile" data-target="replace">
# Start with the image we want to scan
FROM mongo:3.2.1

# Add the microscanner binary
ADD https://get.aquasec.com/microscanner /

# Make it executable
RUN chmod +x /microscanner

# Indicate that the token will be passed in as a build argument
ARG TOKEN

# Run microscanner to scan the image contents
RUN /microscanner ${TOKEN}
</pre>

Run `docker build` to execute the steps in this Dockerfile. 

`docker build --build-arg TOKEN=$token --no-cache .`{{execute}}

* The `--build-arg` parameter passes in an argument that matches the `ARG` command in the Dockerfile
* It's a good idea to used the `--no-cache` argument to force the scan to be re-run even if the image hasn't changed. See the notes for more on why this is necessary.

If microscanner finds a high severity vulnerability, that step will fail, and hence the image build will fail. You should see `The command '/bin/sh -c /microscanner ${TOKEN}' returned a non-zero code: 4` as the last line of the output.

## Scan an image without high severity vulnerabilties

Edit the Dockerfile to scan `mongo:latest` instead of the old `mongo:3.2.1` image.

Run the build command, and this time it should succeed:

`docker build --build-arg TOKEN=$token --no-cache .`{{execute}}

You should see the command output finishes with `Successfully built <image ID>`.

# Notes and next step

Security researchers find new vulnerabilities in existing software all the time. This means that even if you don't change a piece of code, a new vulnerability might be published in it. This means that you need to re-scan your container images on a regular basis even if you don't change their contents, so that the scanner can alert you to any newly published vulnerabilities it might include. Typically at Aqua we find that our enterprise customers scan at least the container images they're using in production on a daily basis.

This is why you need to pass in the `--no-cache` parameter on the `docker build` command, otherwise caching would mean that the scanner wouldn't run if the underlying base image doesn't change.

With the current Dockerfile, you will end up with an image that also includes the `microscanner` executable. In the next step we'll see a couple of approaches you can use to avoid this being included in your final image.
