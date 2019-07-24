You have an image, and soon you're going to run that image as a pod in Kubernetes. First of all you should meet the command line tool for Kubernetes: `kubectl`.

## Interacting with Kubernetes

There is a command line tool `kubectl` for interacting with Kubernetes. You can install a copy of `kubectl` on your laptop, and with the right credentials and configuration information (and network connectivity), it can interact with a Kubernetes cluster wherever it's running.

In this Katacoda scenario there is a single-node Kubernetes cluster running already. You can use `kubectl` to see the Kubernetes node that's running in your Katacoda environment.

`kubectl get nodes`{{execute}}

You should see a single node. If its status is `NotReady`, give it a few seconds and try again. If it's still NotReady, the next command should give some clues.

`kubectl describe nodes`{{execute}}

* The `get` subcommand lists resources of a given type - in this case it was nodes.
* You can get more information about resources with the `describe` subcommand:

In Kubernetes, everything is a resource: nodes, pods, and many other types of object (some of which you'll meet later in this scenario). You can use `kubectl get` and `kubectl describe` on any of these resources.

At the moment there are no pods so the following command won't find any resources:

`kubectl get pods`{{execute}}


Kubernetes is usually configured using YAML files. Here is a YAML file for running the _hello_ image in a pod.

<pre class="file" data-filename="pod.yaml" data-target="replace">
apiVersion: v1
kind: Pod
metadata:
  name: hello-pod
  labels:
    app: hello
spec:
  containers:
  - name: hello
    image: hello:latest
    imagePullPolicy: Never
</pre>

The file format is YAML. If you haven't come across YAML before, [here is an introductory guide](https://circleci.com/blog/what-is-yaml-a-beginner-s-guide).

* The definition uses the Kubernetes API v1
* The `kind` of resource being defined is a Pod
* There is some metadata and a specification for the Pod
  * In the metadata there is a name for the Pod, and a label is also applied.
  * The specification says what is to go inside the pod. In this case the specification is very simple: there is just one container called `hello`, and the image is `hello:latest`. Each container needs a name for identification purposes
  * Setting `imagePullPolicy` to `never` means that the node expects to find the image present. You would be very unlikely to use this setting in a real Kubernetes environment. 


## Applying the YAML file

You can tell Kubernetes to act on the contents of a YAML file with the `apply` subcommand. The next command applies the Pod YAML you defined earlier.

`kubectl apply -f pod.yaml`{{execute}}

Now that the cluster knows about the pod, you can `get` and `describe` information about it:

`kubectl get pods`{{execute}}

You should see that the pod is in running state. If it's still creating, give it a few seconds and try again. You can also find out more information about the pod (whether it is running yet or not) with `kubectl describe`:

`kubectl describe pod hello-pod`{{execute}}

* The name of the pod is as specified in the pod's metadata.
* You'll see information about the pod and about the container within it.
* In the description you should see an IP address. Each pod in Kubernetes gets allocated its own IP address.

Kubernetes performs several actions when it is asked to run a pod:

* It selects a node for the pod to run on. In this scenario there is only one node so it's a very simple choice.
* If the node doesn't already have a copy of the container image for each container in the pod specification, it will pull it from the container registry. (You can force it to always pull the image to make sure it has the most up-to-date version).
* Once pulled, the node can start running the container(s) for the pod.

## Next step and further reading

In the next step you'll confirm that the pod is really running your container image, and then you'll see how Kubernetes can easily run multiple copies of your image.

* The component on each node that runs containers is called the _Kubelet_. You can get an overview of the different Kubernetes components from the [documentation](https://kubernetes.io/docs/concepts/overview/components/).
* [More on Kubernetes scheduling](https://www.oreilly.com/ideas/kubernetes-scheduling-magic-revealed)

You used an `imagePullPolicy` of `Never` for this scenario. This works here because you are building images on the same (virtual) machine that you're running Kubernetes. It's much more common to build the image, store it in an imge registry, and then have Kubernetes nodes pull the image from that registry when they need it.

Did you notice that you didn't have to specify the port that your container is listening on? In Kubernetes you do have the option to define a `containerPort` field which is roughly the equivalent of specifying the container point with the `-p` option in `docker run`. This is really just documentation though: in Kubernetes, any port listening on 0.0.0.0 can receive requests, whether or not it is specified with `containerPort`. From the [Kubernetes API documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/?source=post_page---------------------------#container-v1-core), _"Exposing a port here gives the system additional information about the network connections a container uses, but is primarily informational. Not specifying a port here DOES NOT prevent that port from being exposed. Any port which is listening on the default "0.0.0.0" address inside a container will be accessible from the network."_

### Optional exercise

As an optional exercise you can try pushing the image to the Docker Hub registry and have your Kubernetes node pull it from there. Here's what you need to do:

* Log in to Docker Hub with `docker login`{{execute}}
* [Tag](https://docs.docker.com/engine/reference/commandline/tag/) the image: `docker tag hello:latest *yourname*/hello:latest`{{copy}}
* Push the image to Docker hub: `docker push *yourname*/hello:latest`{{copy}}
* Change the image name in the pod YAML file to `*yourname*/hello:latest
* Remove `imagePullPolicy: Never` from the pod YAML file so that Kubernetes does pull the image from Docker Hub when it intends to run that container
* Delete the existing pod: `kubectl delete pod hello-pod`{{execute}}
* Apply the pod YAML to recreate it with the new definition: `kubectl apply -f pod.yaml`{{execute}}






