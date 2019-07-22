It's very likely that your application code will need to communicate with other containerized code in the same Kubernetes cluster. Kubernetes makes networking your containers together very easy, using DNS. 

In this step you will deploy a service for a database, and modify your app code so that it uses the database. 

## Database service

First create a file:

`touch pg-svc.yaml`{{execute}}

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

## Database container

You're going to use Postgres as a database.

`touch pg-deployment.yaml`{{execute}}

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
 
This deployment uses a `hostPath` volume, which means the containers in this deployment can access a directory on the host node. This will work for our demonstration purposes today, because we only have one node. In a production deployment you would have your containers use [other types of storage](https://kubernetes.io/docs/concepts/storage/volumes/)

The deployment also refers to a ConfigMap, which is a way of passing environment variables into a container. 

`touch pg-cfg.yaml`{{execute}}

<pre class="file" data-filename="pg-cfg.yaml" data-target="replace">
apiVersion: v1
kind: ConfigMap
metadata:
  name: pg-config
data:
  HELLO_DB_HOST: postgres://postgres@postgres/example?sslmode=disable
  HELLO_DB_KIND: postgres
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

`CREATE TABLE hits (total int);`{{execute}}

Quit out of `psql` and out of the container.

`\q`{{execute}}

`exit`{{execute}}

## Go app using the database

Here is a very simple go app that will increment a hit count every time it serves a response.

<pre class="file" data-filename="hello.go" data-target="replace">
package main

import (
  "database/sql"
  "fmt"
  "net/http"
	  

  _ "github.com/lib/pq"
)

var db *sql.DB

func main() {
  db = getDatabase()
  defer db.Close()  
  
	http.HandleFunc("/", handler)
	
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		fmt.Printf("ListenAndServe exited with error %v\n", err)
	}
}

func getDatabase() *sql.DB {
  host := "postgres-svc"
  port := 5432
  user := "postgres"
  dbname := "hello"
  psqlInfo := fmt.Sprintf("host=%s port=%d user=%s "+
    "dbname=%s sslmode=disable",
    host, port, user, dbname)

  db, err := sql.Open("postgres", psqlInfo)
  if err != nil {
    fmt.Printf("Failed to open database: %v\n", err)
    panic(err)
  }
  fmt.Println("Opened database")

  return db  
}

func incrementCount() int {
	var hits int
	row := db.QueryRow("UPDATE hits SET total = total + 1 RETURNING total;")
	err := row.Scan(&hits)
	if err != nil {
		fmt.Printf("Database increment error: %v\n", err)
		return -1
	}
	return hits
}

func handler(w http.ResponseWriter, r *http.Request) {
	hits := incrementCount()
	response := fmt.Sprintf("hits: %d\n", hits)
	w.Write([]byte(response))
}

</pre>

We need to `go get` a package from a Git repository and this requires us to [install `git`](https://stackoverflow.com/questions/36044275/golang-go-get-command-showing-go-missing-git-command-error) into the first stage of the multistage build. This means our Dockerfile looks like this:

<pre class="file" data-filename="hello.go" data-target="replace">
FROM golang:1.12-alpine3.10

# Move to the directory where the source code will live
WORKDIR /go/src/hello

RUN apk add git
# Copy the source code into the current directory
COPY . .

# Get any dependencies, and compile the code
RUN CGO_ENABLED=0 go get -v ./...

# Second stage: start from an empty base image
FROM scratch

# Copy the binary from the first stage
COPY --from=0 /go/bin/hello /

# Tell Docker what executable to run by default when starting this container
ENTRYPOINT ["/hello"]
</pre>

Build the container image.

`docker build -t hello .`{{execute}}

## Create a service for this Go app

You can now create the deployment with your new "hello" web application, and also create a service for it.

`kubectl apply -f deployment.yaml`{{execute}}

`kubectl apply -f service.yaml`{{execute}}

Check that the pods start up alright, noticing that you now have pods for both the hello app and the database service that it is connecting to.

`kubectl get pods`{{execute}}

`kubectl logs -l app=hello`{{execute}}

Get the IP address of the service.

`kubectl get svc hello-svc`{{execute}}

You can use this to make requests to the hello service. Do this several times to check that the number of hits - which is stored in the database, not in the app - goes up each time.

## Notes and extra exercises

The web app is _stateless_ in that it doesn't hold any data itself. All the state that is important to the application - in this case, the number of hits - is stored in a separate database. This means you can have several instances of the web app pod running simultaneously. The database takes care of serializing the increments to the hits table.

* As an extra exercise you could modify the Go app so that its response includes the host name so that you can verify which pods are running
