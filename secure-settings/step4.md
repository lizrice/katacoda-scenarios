The next command runs a job which only runs the tests for master nodes (so there is less output to confuse us!).

`kubectl apply -f kb-master-job.yaml`{{execute}}

Wait until the "kube-bench-master" job is complete

`kubectl get jobs --watch`{{execute}}

Hit <kbd>Ctrl</kbd>+<kbd>C</kbd> and then look at the output.

`clear`{{execute}}
`kubectl logs $(kubectl get pods -l app=kube-bench-master -o name)`{{execute}}

If you scroll back through the results you should see that test 1.1.11 fails.

```
[FAIL] 1.1.11 Ensure that the admission control plugin AlwaysPullImages is set (Scored)
```

Scroll further down to find the remediation advice for this test:

```
1.1.11 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --enable-admission-plugins to
include AlwaysPullImages.
--enable-admission-plugins=...,AlwaysPullImages,...
```

Are you feeling confident? Let's edit that API Server manifest to add the AlwaysPullImage

`/etc/kubernetes/manifests/kube-apiserver.yaml`{{open}}

Find the line that specifies the `--enable-admission-plugins` parameter - it should look like this:

```
    - --enable-admission-plugins=NodeRestriction
```

Add `,AlwaysPullImages`{{copy}} to the end of the line so that it looks like this:

```
    - --enable-admission-plugins=NodeRestriction,AlwaysPullImages
```

Save the file

It will take a few seconds for the API server to restart with its new configuration.

