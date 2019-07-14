Kubernetes has reported that your pod is running. The kubelet has done the conceptual equivalent of `docker run` on your behalf. 

In practice the component that runs containers might be Docker, or it might be an alternative such as RedHat's cri-o. This is called the _runtime_. In this Katacoda environment, and in the Kubernetes cluster that you can run under Docker for Mac/Windows, the runtime is Docker. In fact it's Docker in many production environments. 

You have already seen that the container is running, through the `kubectl describe` command:

`kubectl describe pod hello-pod`{{execute}}

You can also see the container running from Docker's perspective:

`docker ps -f name=hello`{{execute}}

You can compare the container ID to see that they are the same.

## Reconciliation

Kubernetes currently knows that it is supposed to be running a pod that runs the `hello` container image. We can see reconciliation in action by stopping that container.

`docker stop $(docker ps -q -f name=hello)`{{execute}}

Take a look at the Kubernetes pod:

`kubectl get pods`{{execute}}

Kubernetes reconciles the currently running containers with the containers it knows are supposed to be running. If you stop the underlying container it will restart it again.

To really stop the code from running, you need to delete the pod.

`kubectl delete pod hello-pod`{{execute}}

## Deployments

One of the benefits of Kubernetes is the ability to run multiple instances of a pod. The easiest way to do this is with a _deployment_. The following YAML defines a deployment that will run two instances (replicas) of pods that are practically identical to the pod you just ran:

<pre class="file" data-filename="deployment.yaml" data-target="replace">
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: hello
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
      - name: hello
        image: hello:latest
</pre>

* The metadata near the top of the file applies to the deployment object (not the pods)
* The pods take their definition from the `template` part of the YAML definition. This includes the metadata taht will apply to the pods.

The only difference between the template part of this file and `pod.yaml` is that there is no name defined in the pod metadata. The two pods wouldn't be permitted to have the same name anyway. Instead, Kubernetes will autogenerate a name for each pod based on the deployment name plus some random characters.

Apply this deployment:

`kubectl apply -f deployment.yaml`{{execute}}

Check for the pods: 

`kubectl get pods`{{execute}}

You can also get information about the deployment:

`kubectl get deployments`{{execute}}

## Reading logs

When you run containers under Docker, output to stdout and stderr can be read using `docker logs`. There is a very similar approach in Kubernetes:

`kubectl logs`{{execute}}

Since there are two containers, you should see the output showing two different hostnames.

* Read more about [Kubernetes Deployments](https://cloud.google.com/kubernetes-engine/docs/concepts/deployment)
