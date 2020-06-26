Congratulations, you have remediated a security issue on this node!

## Extra information: why should you set AlwaysPullImages?

This setting ensures that when a pod is scheduled to a node, the image is always pulled from the registry even if there is already a locally-cached copy. This ensures that the latest version of that image is going to be used.

Note: This is a good example where the CIS Benchmark is good advice, but it's not the only way to solve a particular security issue. If your YAML files always refer to images by their SHA rather than using a (semantic) tag, you can be sure that they are exactly the version of code that you expect to be running. If you use tags for your images, such as myapp:3.1, there is no way to guarantee that two nodes running with the same image tag are running identical code - the image might have been rebuilt but given the same tag. (This is even more likely if you use the "latest" tag!)


