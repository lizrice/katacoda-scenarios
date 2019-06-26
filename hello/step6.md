You have been running containers in the Katacoda environment, on a virtual machine in the cloud. As an optional extra, if you have Docker installed on your laptop, why not try running your container image directly on your own machine? 

## What's your Docker Hub username? 

The next command prompts you for your username, and stores it in the environment variable `$yourname` for use in later commands.

`read -p 'Enter your Docker Hub username: ' yourname`{{execute}}

If you don't already have a Docker Hub account, you can sign up for a free account at [hub.docker.com](https://hub.docker.com).

## Tag your image

We are going to ask Docker to push your image to your repository on Docker. First we need to tag the existing `hello` image to include the repository name.

`docker tag hello $yourname/hello`{{execute}}

If you list the container images you will see two entries with the same container image ID. In practice they are the same image, with two different tags.

## Log in to Docker Hub

You need to log in to Docker Hub so that you have permission to push your image to your repository. Follow the prompts:

`docker login -u $yourname`{{execute}}

## Push the image to Docker Hub

If you have successfully logged in, you should be able to push the image with the following command:

`docker push $yourname/hello`{{execute}}

## Pull the image to your laptop

Open a terminal on your laptop. In the following commands you'll need to substitute your Docker username. 

Pull a copy of the image to your laptop:

`docker pull *yourname*/hello`{{copy}}

Now you should be able to run it locally:

`docker run -d -P *yourname*/hello`{{copy}}

Check that it is running, and find the host port that has been assigned:

`docker ps`{{copy}}

You should be able to curl to make a request, or even open a browser window and go to the page `localhost:<host port number>`
