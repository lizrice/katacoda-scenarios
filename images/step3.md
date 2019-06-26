Now it's time to run your app inside a container. 

`docker run --rm -d -p 18080:8080 hello`{{execute}}

## Check you can access it

Note that we have mapped port 8080 inside the container to port 18080 on the host, so you can send a request to this container on port 18080.

`curl localhost:18080`{{execute}}

or:

`curl https://[[HOST_SUBDOMAIN]]-18080-[[KATACODA_HOST]].environments.katacoda.com/`{{execute}}


We copied the `text/response` file so that it's part of the container image at the location `/text/response`. This container image is self-contained - wherever you run it, the file exists at `/text/response` from the container's perspective. Note that this *isn't* `/text/response` on the host where you're running the container. The filesystem in the container is different from what the host sees.

You can verify this is true by using the editor panel above to change the contents of the `text/response` file. Even though the application reads the file every time you make a request, changing the file here doesn't have any effect. This is because the application is reading the file from the container's own version of the filesystem.

In the next step, we'll see how you can mount a volume into a container so that it *can* see files on the host filesystem. 