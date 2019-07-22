
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
        imagePullPolicy: Never
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

You can also follow the logs as they are generated:

`kubectl logs -l app=hello -f`{{execute}}

Quit out of this with Ctrl-C when you are done, and delete the deployment with the following command, which tells Kubernetes to delete everything that was specified in the YAML file:

`kubectl delete -f deployment.yaml`{{execute}}

## Make your app a web service

So far you have a very simple app that does nothing but generate logs. Let's see what happens if you have an app that responds to web traffic. Modify your Go code so that instead of just logging the hostname message, it responds to an HTTP request with the same information.

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "fmt"
  "net/http"
  "os"
)

func main() {
    hostname, _ := os.Hostname()
    response := fmt.Sprintf("hello, my hostname is %v\n", hostname)
    fmt.Printf(response)

	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte(response))
	})

	err := http.ListenAndServe(":8080", nil)
	if err != nil {
	  fmt.Printf("serving: %v\n", err)
	  os.Exit(1)
	}    
}
</pre>

Rebuild the app:

`docker build -t hello .`{{execute}}

Recreate the deployment, which will use the new web-app version of the image this time. (It's not great practice to keep re-using the same name and tag for the image when it is performing a different function, but it saves having to modify the YAML file for this lesson.)

`kubectl apply -f deployment.yaml`{{execute}}

After a few seconds you should find there are two pods running:

`kubectl get pods`{{execute}}

Each pod is assigned its own IP address. The IP address is included as part of the detailed information you get from `kubectl describe` on a pod.

`kubectl describe pods | grep IP`{{execute}}

Pick either of these IP addresses and you can make a curl request to it. Recall that the app is listening on port 8080:

`read -p "Enter a pod IP address: " address`{{execute}}

`curl $address:8080`{{execute}}

Try with the other address as well.

## Load balancing

When you have multiple instances of a container image, you'll typically want to load balance requests to them so that they can share the load of incoming requests. As you'll see in the next step, this is achieved in Kubernetes with a service.

## Next steps and further reading

Deployments make it easy to run an arbitrary number of pods. In the next step you'll see how these pods can be grouped together to provide a Service that other pods can interact with.

Before you move on, if you have time you might want to:

* Read more about [Kubernetes Deployments](https://cloud.google.com/kubernetes-engine/docs/concepts/deployment)
* Read more about [using labels as a selector in Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors)

If you're going to use Kubernetes from the command line, this [cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) is very helpful.