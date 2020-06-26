At this stage the kube-bench job should have completed. Let's clear the screend then run a command that gets the logs:

`clear`{{execute}}
`kubectl logs $(kubectl get pods -l app=kube-bench -o name)`{{execute}}

Scroll up to the top of the output to see a list of tests that have passed, failed, or for which there is a warning. [WARN] generally means that it's not possible to run the test without manual intervention.

Scroll further down and you will see some remediation advice which gives information on what to do to change settings so that the tests pass.  Then you'll see a summary showing how many tests passed and failed.

Keep scrolling and you might notice that there are multiple sections of tests each with their own results, remediation and summary. This is because there are different sets of tests that can run on different types of node.

In the next step, you will just run the master node tests, and you will fix one of the configuration settings to improve the number of passing tests.

