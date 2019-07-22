It's very likely that your application code will need to communicate with other containerized code in the same Kubernetes cluster. Kubernetes makes networking your containers together very easy, using DNS. 

In this step you will deploy a service for a database, and modify your app code so that it uses the database.

The application will return the number of requests or "hits" that it has received. The number of hits is maintained in a database.

## Database service

You're going to use Postgres as the database. Start with the service YAML definition for the postgres service.

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

You can create this now, even though there are no pods that match the `app=postgres` selector yet. This is one of the consequences of Kubernetes' approach to reconciliation; Kubernetes will continue to look for pods that match this selector, and will add them to the Service as and when they come available.

`kubectl apply -f pg-svc.yaml`{{execute}}

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

### Initialize the database

As a one-off exercise you need to initialize the Postgres database. To do this you will "exec in" to the postgres container in the pod - if you like, you can think of this as like SSH-ing into the container.

Find the ID of the postgres container pod.

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

## Next steps

You now have a Kubernetes service running Postgres. In the next step you will write a Go application that will use it. 