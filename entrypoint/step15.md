Here is a Dockerfile that will build an image based on Alpine, specifying `ls` as the entrypoint.

<pre class="file" data-filename="Dockerfile" data-target="replace">

FROM alpine

ENTRYPOINT ["ls"]

CMD ["/bin"]
</pre>

Build this image.

`docker build -t ls:cmd .`{{execute}}

When you run this image, 
* `ls` will be run by default because of ENTRYPOINT
* `/bin` is added as an argument because of CMD

`docker run -t ls:cmd`{{execute}}

If you add your own arguments, these will replace what was specified in CMD. So for example the following lists the contents of `/tmp` and not the contents of `/bin`.

`docker run -t ls:cmd /tmp`{{execute}}

## Next step

A good way to think of this is this:

* If your container image is designed to run one executable (all, or nearly all the time) then specify that executable using ENTRYPOINT

* If your container image is designed as a multi-purpose image, you can specify CMD for the default behavior and omit ENTRYPOINT so that it's easy for the user to specify the executable they want to run