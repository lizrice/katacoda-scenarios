There are two forms for specifying commands that are run in Dockerfiles (e.g. in the RUN, CMD and ENTRYPOINT commands)

* The *shell* form starts the executable in a shell: it's the equivalent of `sh -c <executable>`
* The *exec* form runs the executable directly: it's the equivalent of simply running `<executable>`

RUN commands are executed during the build process. Let's use that to explore these different forms.

## Shell form

The shell form is specified directly in the command. Here's an example:

<pre class="file" data-filename="Dockerfile" data-target="replace">
FROM alpine

RUN echo this is the shell form
</pre>

`docker build -t echo:shell .`{{execute}}

In the shell form, shell parameters will be expanded:

<pre class="file" data-filename="Dockerfile" data-target="replace">
FROM alpine

RUN echo $HOME
</pre>

`docker build -t echo:shell .`{{execute}}

## Exec form

The exec form is specified as a JSON array, like this: 

<pre class="file" data-filename="Dockerfile" data-target="replace">
FROM alpine

RUN ["echo", "this", "is", "the exec form"]
</pre>

`docker build -t echo:exec .`{{execute}}

In the exec form, shell variables are not expanded.

<pre class="file" data-filename="Dockerfile" data-target="replace">
FROM alpine

RUN ["echo", "$HOME"]
</pre>

`docker build -t echo:exec .`{{execute}}
