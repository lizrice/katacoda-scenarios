In this step you'll get your simple Go application running in Kubernetes and Skaffold.

## Write a pod manifest

You will need a YAML file to define the pod which will run your application.

<pre class="file" data-filename="k8s-pod.yaml" data-target="replace">
apiVersion: v1
kind: Pod
metadata:
  name: hello-pod
spec:
  containers:
  - name: hello-container
    image: hello
</pre>

The pod consists of one container which will run the `hello` image. 

## Write a skaffold.yaml file

The `skaffold.yaml` file describes the stages that Skaffold is going to take responsibility for.

<pre class="file" data-filename="skaffold.yaml" data-target="replace">
apiVersion: skaffold/v1beta11
kind: Config
build:
  artifacts:
  - image: hello
deploy:
  kubectl:
    manifests:
      - k8s-*
</pre>

This simple Skaffold configuration has two stages.

**Build**

The build step will generate the `hello` container image by doing a Docker build. By default the build context is the current directory, and we already have a Dockerfile in this directory which describes how to build the image. The `image` field in the `skaffold.yaml` file tells skaffold to tag it with the name `hello`.

Because you are running a local Kubernetes cluster in minikube, Skaffold skips the step of pushing images to a registry.

**Deploy**

The deploy step will deploy any Kubernetes manifests with names starting `k8s-`. This includes the `k8s-pod.yaml` file you just created.

## Quiz

You have built an image with the repository name `hello` and the default tag of `latest`. 

>>Q1 Which of these would **not** run that image?<<
( ) `docker run -t hello:latest`
( ) `docker run hello`
(*) `docker run hello-container`
( ) `docker run -t hello`

You have written a Dockerfile, a skaffold.yaml file and a pod manifest.

>>Q1: which of these files would you have to modify if you wanted to change the image name?<<
[ ] Dockerfile
[*] skaffold.yaml
[*] k8s-pod.yaml

## Next step 

In the next step let's get skaffold to run your app!