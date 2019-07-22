Earlier in this scenario you created a deployment with two pods. If you recall, this was set up by defining that there would be a _replica count_ of 2 in deployment.yaml. 

Check that there are currently two pods in the deployment:

`kubectl get pods`{{execute}}

Let's scale up this deployment:

`kubectl scale --replicas=5 deployment/hello`{{execute}}

Check the number of pods:

`kubectl get pods`{{execute}}

These pods are automtically made part of the `hello-svc` service, and you should see responses from them if you repeatedly make requests to the service:

`curl $clusterip:30000`{{execute}}

Try scaling the deployment down again. When you're done, delete the *hello* deployment.

`kubectl delete deployment hello`{{execute}}

# Further reading and next steps

This scenario has given you a basic introduction to running a Go application in Kubernetes.