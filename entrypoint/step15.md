Here is a Dockerfile that will build an image based on Alpine, specifying `ls` as the entrypoint and specifying `/bin` as the default argument that is passed to the `ls`executable.

<pre class="file" data-filename="Dockerfile" data-target="replace">
FROM alpine

ENTRYPOINT ["ls"]

CMD ["/bin"]
</pre>

Build this image.

`docker build -t ls:entry-cmd .`{{execute}}

When you run this image,

* `ls` will be run by default because of ENTRYPOINT
* `/bin` is added as an argument because of CMD

`docker run -t ls:entry-cmd`{{execute}}

If you add your own arguments, these will replace what was specified in CMD. So for example the following lists the contents of `/lib` and not the contents of `/bin`.

`docker run -t ls:entry-cmd /lib`{{execute}}

## Notes and next step

With both ENTRYPOINT and CMD specified in the same image you can see how the CMD arguments get appended to ENTRYPOINT.
