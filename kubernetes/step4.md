
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
        imagePullPolicy: never
</pre>

* The metadata near the top of the file applies to the deployment object (not the pods)
* The pods take their definition from the `template` part of the YAML definition. This includes the metadata that will apply to the pods.

The only difference between the template part of this file and `pod.yaml` is that there is no name defined in the pod metadata. The two pods wouldn't be permitted to have the same name anyway. Instead, Kubernetes will autogenerate a name for each pod based on the deployment name plus some random characters.

Apply this deployment:

`kubectl apply -f deployment.yaml`{{execute}}

Check for the pods: 

`kubectl get pods`{{execute}}

You can also get information about the deployment:

`kubectl get deployments`{{execute}}

## Reading logs from all the pods in a deployment

In the previous step, you used `kubectl logs` to read the logs from a pod. Now you have two pods running identical code. You could read them individually by specifying the pod name as before (`kubectl logs _pod_name_`), but there is also an easy way to see the logs from across all the instances at once. Each pod has the _label_ `app: hello` and you can request logs from all pods with a given label.

`kubectl logs -l app=hello`{{execute}}

When a label is used in this way to select all the matching pods it is called a _label selector_. Since there are two containers, you should see the output showing two different hostnames.

## Next steps and further reading

Deployments make it easy to run an arbitrary number of pods. In the next step you'll see how these pods can be grouped together to provide a Service that other pods can interact with.

* Read more about [Kubernetes Deployments](https://cloud.google.com/kubernetes-engine/docs/concepts/deployment)
* Read more about [using labels as a selector in Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors)