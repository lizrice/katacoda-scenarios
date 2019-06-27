The answer is that by default, output from *stdout* and *stderr* are sent to Docker logs. To see these logs we can simply run the command `docker ps <container ID>`. But we'll need to get hold of the container ID first.

## Get container ID

To retrieve the logs we'll need the identity of the running container:

`docker ps`{{execute}}

Here's an example of the kind of output you should see:

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS  NAMES
63e325cf0886        hello               "/hello"            5 seconds ago       Up 3 seconds        0.0.0.0:18080->8080/tcp  gifted_ardinghelli
```

You can identify the container by its

* **container ID** - in this example it's 63e325... (recall that you only need to use enough of the first few hex digits to unqiuely identify the container)
* **name** - in the example it's gifted_ardinghelli

The container ID and name you'll see will be different.

Here's a shortcut to getting the container ID for the most recently run container:

`docker ps -ql`{{execute}}

* The -q flag returns just the numeric ID
* The -l flag returns the most recently run container

The `-q` flag is neat, because you can use the ID as input to other docker container commands, like `docker logs`.

## Get the logs

The following command gets the logs from the most recently run container:

`docker logs $(docker ps -ql)`{{execute}}

When you run this you should see any output that has been sent to stdout or stderr. 

## Optional extra task

Want to confirm that stderr also gets written to Docker logs? You could do easily do this by modifying your app.

* Edit `hello.go` to add a line that prints something to stderr. You can do this with `fmt.Fprintf(os.Stderr, "your text here")`:  `hello.go`{{open}}
* Recompile hello.go: `CGO_ENABLED=0 go build -o hello hello.go`{{execute}}
* Rebuild the container: `docker build -t hello .`{{execute}}
* Stop the currently running instance of the container: `docker stop $(docker ps -ql)`{{execute}}
* Run the container again. `docker run --rm -d -p 18080:8080 hello`{{execute}}
* Check it's working: `curl localhost:18080`{{execute}}
* Get the logs: `docker logs $(docker ps -ql)`{{execute}}

Did you see the output for both stderr and stdout in the logs?

You might think that there are a lot of manual steps between editing your Go code and running it. We'll see some ways to automate some of these intermediate steps in another scenario. 

# Next step

Remember that your app is reading its response from a text file that has been built into the container image? In the next step we'll see how you can mount directories into a container so that the container can access files that are stored on the host.
