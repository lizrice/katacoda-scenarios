You can now create the deployment and service for your "hello" web application.

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

`kubectl apply -f deployment.yaml`{{execute}}

Check that the pods start up alright, noticing that you now have pods for both the hello app and the database service that it is connecting to.

`kubectl get pods`{{execute}}

Now create a service for the web application: 

<pre class="file" data-filename="service.yaml" data-target="replace">
apiVersion: v1
kind: Service
metadata:
  name: hello-svc
spec:
  type: NodePort
  ports:
  - targetPort: 8080
    port: 30000
  selector:
    app: hello
</pre>

## Next steps 

In the next step you'll see if it is working as expected! 