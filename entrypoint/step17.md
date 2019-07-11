This Dockerfile that will build an image based on Alpine, specifying `ls` as the CMD.

<pre class="file" data-filename="Dockerfile" data-target="replace">
FROM alpine

CMD ["ls"]
</pre>

Build this image.

`docker build -t ls:cmd .`{{execute}}

Running this image will execute `ls`.

`docker run -t ls:cmd`{{execute}}

This behaves the same as the entrypoint version if you don't pass any additional arguments:

`docker run -t ls:entrypoint`{{execute}}

As before, if you add your own arguments, these will *replace* what was specified in CMD. So this will fail:

`docker run -t ls:cmd /lib`{{execute}}

But this will work:

`docker run -t ls:cmd ls /lib`{{execute}}

## Notes and next step

A good rule of thumb is that if your container image always (or nearly always) runs one executable, specify that as the ENTRYPOINT. Add any default arguments with CMD. Avoid specifying CMD without specifying ENTRYPOINT.
