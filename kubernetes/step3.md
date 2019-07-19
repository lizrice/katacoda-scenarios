Kubernetes has reported that your pod is running. The kubelet has done the conceptual equivalent of `docker run` on your behalf. 

In practice the component that runs containers might be Docker, or it might be an alternative such as RedHat's cri-o. This is called the _runtime_. In this Katacoda environment, and in the Kubernetes cluster that you can run under Docker for Mac/Windows, the runtime is Docker. In fact it's Docker in many production environments. 

You have already seen that the container is running, through the `kubectl describe` command:

`kubectl describe pod hello-pod`{{execute}}

In the `Containers` section of the `describe` output you should see the container called `hello`. It includes the Container ID, as well as the name and ID of the image this container was created from. 

You can also see the container running from Docker's perspective:

`docker ps -f name=hello`{{execute}}

You can compare the container ID to see that they are the same.

## Reading logs

When you run containers under Docker, output to stdout and stderr can be read using `docker logs`. There is a very similar approach in Kubernetes:

`kubectl logs hello-pod`{{execute}}

The Go program you are running outputs the name of the host that it's running on. Notice that in Kubernetes, this is automatically set to the pod name.

## Reconciliation

Kubernetes currently knows that it is supposed to be running a pod that runs the `hello` container image. We can see reconciliation in action by stopping that container.

`docker stop $(docker ps -q -f name=hello)`{{execute}}

Take a look at the Kubernetes pod:

`kubectl get pods`{{execute}}

Kubernetes reconciles the currently running containers with the containers it knows are supposed to be running. If you stop the underlying container it will restart it again.

To really stop the code from running, you need to delete the pod.

`kubectl delete pod hello-pod`{{execute}}

Check that the pod has stopped:

`kubectl get pods`{{execute}}

## Next step

You have already seen how to get Kubernetes to run a container for you in a pod. If the container stops for any reason, Kubernetes will start a new instance in order to keep itself in line with the declarative definition you supplied.

In the next step you'll see how to run multiple instances of a pod by using a _Deployment_.
