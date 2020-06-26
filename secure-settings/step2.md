Now that the cluster is ready, you can run kube-bench. There is a YAML file prepared that will run kube-bench on this cluster.

`kubectl apply -f kb-job.yaml`{{execute}}

This will start a job called "kube-bench". You can run the following command to watch the job status:

`kubectl get jobs --watch`{{execute}}

Hit <kbd>Ctrl</kbd>+<kbd>C</kbd> once the job is complete.

In the next step you will look at the kube-bench results.