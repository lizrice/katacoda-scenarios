In this step we're going to create a different response file, and we're going to make it accessible to the container so that it can read the file directly from the host, using a mount. 

## Create a new response file

`touch different/response`{{execute}}

Copy the contents into that response file. 

<pre class="file" data-filename="different/response" data-target="replace">
This is a different response
</pre>

Note that this file exists at `different/response`, but the application code still expects to read the file from `text/response`.

## Mount the directory

Run the container, but this time use the `-v` parameter to mount the `different` directory on the host so that it appears to the container at the location `text`.

`docker run -d --rm -v "$(pwd)/different":/text -p 18080:8080 hello`{{execute}}

Check the response:

`curl localhost:18080`{{execute}}

## Change the response

Try editing the contents of `different/response` (which is a file on your host) you should see a different response from the web server in the container, without having to restart the container.

The container is able to access that file because that directory is mounted in.

Also note that the container image still has the text file built into it at the location `text/response` but this is overwritten by the mount. 