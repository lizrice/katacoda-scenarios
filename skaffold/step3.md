It's time to start skaffold:

`skaffold dev`{{execute T1}}

* You should see the skaffold build the container with the same kind of output that you would see if you ran `docker build`
* Then there are messages telling you about deploying the pod to Kubernetes
* Finally you will start to see logs from the hello application being displayed on the screen

## Edit the application

Edit the `hello.go` file to change the message that gets printed out. In this Katacoda environment the file will save automatically.

Skaffold is watching for changes, and it rebuilds and redeploys the application when it spots your change. You will soon see the log output changing to your new message.

## Next steps and further reading

This shows how Skaffold can be used to automate the steps needed to update a deployment when you make changes to your Go code.

Because Skaffold uses Dockerfiles to build images, it's not limited to Go - you can use it with other languages as well. You can find out more about Skaffold on its [website](https://skaffold.dev).
