After a few seconds, check that the API server is up and running again.

`kubectl get nodes`{{execute}}

You need to delete the previous job so that you can run it again.

`kubectl delete job kube-bench-master`{{execute}}

Run the test again

`kubectl apply -f kb-master-job.yaml`{{execute}}

Wait for it to complete

`kubectl get jobs --watch`{{execute}}

Hit <kbd>Ctrl</kbd>+<kbd>C</kbd> once the job is complete, and then view the results.

`clear`{{execute}}
`kubectl logs $(kubectl get pods -l app=kube-bench-master -o name)`{{execute}}

This time, you should see that test 1.1.11 has passed.

