It's important to scan your container images for known vulnerabilities. These are flaws in the packages your code depends on, that could be exploited by an attacker.

## Why you should scan container images

A vulnerability scanner looks at the versions of all the packages in a container image to determine whether they contain any known vulnerabilities. If your image only holds your own custom binary code - which is often the case if you're building an image from scratch with a self-contained binary built from, say, Golang code - then there are no package dependencies to worry about. So why would you still need to worry about scanning your images? 

In many cases you'll be running third-party images alongside your own custom code. For example you may be using databases, message queues, logging, and other components that are delivered and deployed in the form of container images.

It's a good idea to scan these third-party images you're running in your deployment to make sure they don't include vulnerable packages.

Also, even if you and your team are writing self-contained binary executables, it's possible that other people in your organisation are using languages like Java, Ruby or Python, with dependencies on third-party packages. It's good practice to scan all images as part of a CI/CD pipeline, to be sure you are checking everything that's making its way to your production deployment.

## Vulnerability scanning with MicroScanner

There are several vulnerability scanners for container images - some open source, some free, and some commercial. In this section we'll look at scanning container images for vulnerabilities with the free tool, MicroScanner. Microscanner is very simple to incorporate into your build processes because it's added as a few steps in your Dockerfile.

- Adding MicroScanner to your Dockerfile
