In this scenario you will run kube-bench as a Kubernetes job.

You might need to wait a few moments before the Kubernetes cluster is ready. Run the following command:

`watch kubectl get nodes`{{execute interrupt}}

Wait until the node status is "Ready", and then hit <kbd>Ctrl</kbd>+<kbd>C</kbd> to quit that command.