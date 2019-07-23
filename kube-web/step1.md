It's very likely that your application code will need to communicate with other containerized code in the same Kubernetes cluster. Kubernetes makes networking your containers together very easy, using DNS. 

In this step you will deploy a service for a database, and modify your app code so that it uses the database.

The application will return the number of requests or "hits" that it has received. The number of hits is maintained in a database.

## Database container

Here's a deployment file that will run Postgres for you.

<pre class="file" data-filename="pg-deployment.yaml" data-target="replace">
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: postgres
spec:
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - image: "postgres:10.1"
        name: postgres
        envFrom:
        - configMapRef:
            name: pg-config
        ports:
        - containerPort: 5432
          name: postgres
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
      volumes:
      - name: postgres-storage
        hostPath:
          path: /var/lib/pg/data
</pre>
 
* You can use a pre-existing container image for Postgres
* This file refers to a configMap - this is coming up next
* This deployment uses a `hostPath` volume, which means the containers in this deployment can access a directory on the host node. This will work for our demonstration purposes today, because we only have one node. In a production deployment you would have your containers use [other types of storage](https://kubernetes.io/docs/concepts/storage/volumes/)

The deployment refers to a ConfigMap, which is a way of passing environment variables into a container. Let's define that next. 

<pre class="file" data-filename="pg-cfg.yaml" data-target="replace">
apiVersion: v1
kind: ConfigMap
metadata:
  name: pg-config
data:
  PGDATA: /var/lib/postgresql/data/pgdata
  POSTGRES_USER: postgres
</pre>

Create the ConfigMap and the Postgres deployment:

`kubectl apply -f pg-cfg.yaml`{{execute}}

`kubectl apply -f pg-deployment.yaml`{{execute}}

If you get an error, it may be that Kubernetes has not yet started in your environment. Wait for `kubectl get nodes`{{execute}} to show you that there is a node in `Ready` state - it could take a minute or two.

### Initialize the database

As a one-off exercise you need to initialize the Postgres database. To do this you will "exec in" to the postgres container in the pod - if you like, you can think of this as like SSH-ing into the container.

Check that there is a postgres pod running:

`kubectl get pods`{{execute}}

Find the ID of the postgres pod.

`export pod=$(kubectl get pods -l app=postgres --no-headers -o custom-columns=":metadata.name")`{{execute}}

`kubectl exec -it $pod sh`{{execute}}

You are now in a shell in the Postgres container. For the purposes of this demonstration you're going to manually create the database called "hello" that your Go app is going to use:

`createdb -U postgres hello`{{execute}}

And still inside the Postgres container you can manually create a "hits" table inside this database.

`psql -U postgres`{{execute}}

Connect to the *hello* database:

`\c hello`{{execute}}

Create and initialize the hits table in this database:

`CREATE TABLE hits (total int);`{{execute}}

`INSERT INTO hits (total) VALUES (0);`{{execute}}

Quit out of `psql` and out of the container.

`\q`{{execute}}

`exit`{{execute}}

## Database service

Postgres is running in a container in a pod, and that pod has an IP address which you can see if you get the details for it with `kubectl describe`:

`kubectl describe pods -l app=postgres`{{execute}}

This IP address is dynamically allocated each time a pod is created. When you write your web application, it is going to need to connect to the database pod, but it won't know the IP address. Instead, we will create a service, and the application can refer to the service by name.

<pre class="file" data-filename="pg-svc.yaml" data-target="replace">
apiVersion: v1
kind: Service
metadata:
  name: postgres-svc
spec:
  type: NodePort
  ports:
  - port: 5432
  selector:
    app: postgres
</pre>

Create this service:

`kubectl apply -f pg-svc.yaml`{{execute}}

See everything that is up and running with `kubectl get all`{{execute}}

In fact, this is just showing you everything that is running in the default namespace. Namespaces are a way of group resources together - for example, some organizations use namespaces to separate the resources that apply to different applications, or that are owned by different teams. You'll see another namespace shortly.

## Next steps

You now have a Kubernetes service running Postgres. In the next step you will write a Go application that will use it. 