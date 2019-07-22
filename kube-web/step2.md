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

## Kubernetes service resolution

Notice that the hostname for the database is hard-coded to `postgres-svc` in the first line of the `getDatabase()` function. Kubernetes uses DNS to resolve host names to IP addresses within the cluster, in much the same way that domain names are resolved to IP addresses across the internet. 

We need to `go get` a package from a Git repository and this requires us to [install `git`](https://stackoverflow.com/questions/36044275/golang-go-get-command-showing-go-missing-git-command-error) into the first stage of the multistage build. This means our Dockerfile looks like this:

<pre class="file" data-filename="Dockerfile" data-target="replace">
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

## Next steps and further reading

In the next step you will deploy your app under Kubernetes. 

Before you move on, if you like you can take a look at the Kubernetes DNS service components. The Kubernetes DNS service runs as a pod within Kubernetes, but within a separate *namespace* called `kube-system`. You can see the pods in this namespace with the following command:

`kubectl get pods -n kube-system`{{execute}}

Read more about [Kubernetes namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).