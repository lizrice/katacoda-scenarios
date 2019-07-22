A Kubernetes Service groups together a collection of pods and makes them accessible as a service.

<pre class="file" data-filename="service.yaml" data-target="replace">
apiVersion: v1
kind: Service
metadata:
  name: hello-svc
spec:
  type: NodePort
  ports:
  - port: 8080
    nodePort: 30000
  selector:
    app: hello
</pre>

Note that the deployment that you ran earlier still exists, as do the pods that the deployment specifies. Check that they are still there:

`kubectl get pods`{{execute}}

Now create the service:

`kubectl apply -f service.yaml`{{execute}}

* The `type` of the service you're using here is `NodePort`. 
* This service maps a request to the host's port 30000 to port 8080 on any of the service pods.
* The service uses the `app=hello` label as a selector to identify the pods that it will load balance between.

Check that the service exists:

`kubectl get services`{{execute}}

This will show you the IP address that has been allocated to this service. 

Try making a request to the service:

`curl <IP address>:30000`{{execute}}

If you do this a few times you should see that responses could come from either of the pods behind the service.

# Next steps and further reading

In the next step you will scale the number of pods up and down.

* Read more about Kubernetes [Services](https://kubernetes.io/docs/concepts/services-networking/service/)
* See how some [environment variables are automatically defined](https://kubernetes.io/docs/concepts/services-networking/service/#discovering-services) to make it easy for application code to find other services. Be careful - if you define these env vars yourself their values will get overwritten!
