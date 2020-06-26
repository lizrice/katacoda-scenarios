In this scenario you will run kube-bench as a Kubernetes job.

You might need to wait a few moments before the Kubernetes cluster is ready. Run the following command:

`watch kubectl get nodes`{{execute}}

This can take a minute or two, so please be patient. At first you could see a message about not being able to connect to localhost:8080, and then you'll see the status of the master node.

Wait until the node status is "Ready", and then hit <kbd>Ctrl</kbd>+<kbd>C</kbd> to quit that command.