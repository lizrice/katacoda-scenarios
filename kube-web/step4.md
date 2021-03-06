You should now have services for the postgres database and for the web app (plus the kubernetes service itself):

`kubectl get services`{{execute}}

This will show you the *Cluster IP* address that has been allocated to each service. You can use this address to make requests to the service. You are going to make curl requests to the *hello* web application so let's find its IP address.

`read -p "Enter the cluster IP address for hello-svc: " clusterip`{{execute}}

Try making a request to the service:

`curl $clusterip:30000`{{execute}}

If you do this a few times you should see that responses could come from either of the pods behind the service.

## Notes and extra exercises

The web app is _stateless_ in that it doesn't hold any data itself. All the state that is important to the application - in this case, the number of hits - is stored in a separate database. This means you can have several instances of the web app pod running simultaneously. The database takes care of serializing the increments to the hits table.

* As an extra exercise you could modify the Go app so that its response includes the host name so that you can verify which pod is handling each request.
* You could also try scaling the deployments of each service up and down with `kubectl scale deployment _deployment_name_ --replicas=_replica_count`{{copy}}

The ability to scale services independently to cope with demand is one of the features of Kubernetes that makes it ["cloud native"](https://github.com/cncf/toc/blob/master/DEFINITION.md).
