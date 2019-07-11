The ENTRYPOINT and CMD commands in Dockerfiles can be a source of confusion. Let's explore how they work. 

## ENTRYPOINT specifies the default executable

Here is a Dockerfile that will build an image based on Alpine, specifying `ls` as the entrypoint.

<pre class="file" data-filename="Dockerfile" data-target="replace">

FROM alpine

ENTRYPOINT ["ls"]
</pre>

Build this image.

`docker build -t ls:entrypoint .`{{execute}}

When you run this image, `ls` will be run by default. You can specify additional parameters at the end of your `docker run` command and they will be appended to the entrypoint:

`docker run -t echo:entrypoint /bin`{{execute}}

`docker run -t echo:entrypoint -ltr`{{execute}}

`docker run -t echo:entrypoint`{{execute}}



## Next step

At this stage you have a compiled Go binary and a text file that it reads from. In the next step we'll build a container image that includes both the binary and the text file.
