The ENTRYPOINT and CMD commands in Dockerfiles can be a source of confusion. Let's explore how they work. 

## ENTRYPOINT specifies the default executable

Here is a Dockerfile that will build an image based on Alpine, specifying `ls` as the entrypoint.

<pre class="file" data-filename="Dockerfile" data-target="replace">

FROM alpine

ENTRYPOINT ["ls"]
</pre>

Build this image.

`docker build -t ls:entrypoint .`{{execute}}

* In `docker build`, the `-t` parameter specifies the tag for the image

When you run this image, `ls` will be run by default. You can specify additional parameters at the end of your `docker run` command and they will be appended to the entrypoint:

`docker run -t ls:entrypoint`{{execute}}

* In `docker run`, the `-t` parameter allocates a pseudo-tty for the container, which means we can see the output

`docker run -t ls:entrypoint /bin`{{execute}}

`docker run -t ls:entrypoint -ltr`{{execute}}

## Overriding the entrypoint

Because the entrypoint is set up as `ls`, if you want to run a different executable in this container you'll have to explicitly override with the `--entrypoint` parameter.

`docker run -t  --entrypoint echo ls:entrypoint hello`{{execute}}

## Next step

Next we'll see what happens if we add a CMD command to the Dockerfile. 