In this step we're going to create a different response file, and we're going to make it accessible to the container so that it can read the file directly from the host, using a mount. 

## Create a new response file

`mkdir different`{{execute}}

`touch different/response`{{execute}}

`different/response`{{open}}

Copy the contents into that response file.

<pre class="file" data-filename="different/response" data-target="replace">
This is a different response
</pre>

Note that this file exists at `different/response`, but the application code still expects to read the file from `text/response`.

## Mount the directory

Run the container, but this time use the `-v` parameter to mount the `different` directory on the host so that it appears to the container at the location `text`.

`docker run -d --rm -v "$(pwd)/different":/text -p 18081:8080 hello`{{execute}}

Check the response:

`curl localhost:18081`{{execute}}

## Change the response

Try editing the contents of `different/response` (which is a file on your host) you should see a different response from the web server in the container, without having to restart the container.

<pre class="file" data-filename="different/response" data-target="replace">
And this is another response altogether
</pre>

`curl localhost:18081`{{execute}}

The container is able to access that file because that directory is mounted in.

Also note that the container image still has the text file built into it at the location `text/response` but this is overwritten by the mount.

# Notes and further reading

If you have permission to run Docker commands, there is nothing stopping you from mounting *any* directory from the host to make it accessible from the container. This can be a security risk.

* It's possible to [mount directories as read-only](https://docs.docker.com/storage/volumes/#use-a-read-only-volume) so that the container can't affect the contents.
* Mounting a sensitive directory like `/etc` or even the root directory into a container can allow a user to escalate their privileges. See an example in [this video](https://youtu.be/ltrV-Qmh3oY?t=700) (This demo uses Kubernetes, but the directory mounting mechanism works in just the same way you have used above).

# Next step

In the next step you'll see how to pass environment variables into your application.
