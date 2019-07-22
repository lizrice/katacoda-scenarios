
<pre class="file" data-filename="service.yaml" data-target="replace">
apiVersion: v1
kind: Service
metadata:
  name: hello-svc
  labels:
    app: hello
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30080
  selector:
    app: hello
</pre>

# Next steps and further reading

* Read more about Kubernetes [Services](https://kubernetes.io/docs/concepts/services-networking/service/)