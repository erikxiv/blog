---
author: erikxiv
categories: erikxiv
layout: post
cover: false
navigation: true
layout: post
title: Docker workflow revisited
date: '2015-07-10 08:56:34'
subclass: post
tags:
- docker
- workflow
- emberjs
- gulp
---

> In the [previous post](http://blog.erikxiv.com/workflow-to-the-test/) I managed to verify the feasibility of my [home project workflow](http://blog.erikxiv.com/home-project-workflow/), albeit with a few drawbacks coming to light. In this post I will address one of these; the need for installations on my local host.

After all, one of the reasons for going the extra [Docker](docker.com) mile is to minimize dependencies and make our lives simpler, so a workflow requiring both Docker to be installed AND the same toolset as before isn't really helping as much as it could.

### Ember, again

I'll continue with the same simplistic example, a generated [Ember](emberjs.com) application. The difference this time is that I would like to **(1)** not use [ember-cli](http://www.ember-cli.com/) locally, e.g. not have ember installed on my machine, and **(2)** to still utilize the Ember workflow, using the supplied tooling around Ember to **create** the application, generate scaffolding, **test** the application and **run** it.

### Software versions

Another thing I skimped on in the last post was strategy for versioning (of software libraries). As the [strategy](http://blog.erikxiv.com/home-project-workflow/) calls for **Timelessness** it is not acceptable that a project will work differently (or not at all) next year due to changes made in dependencies. The strategy for this is simple, use specific versions of all dependencies such as Docker images and libraries/applications used.

> Example: When configuring which Docker image to work with, I should always specify a tagged version, not "latest" (see [here](https://medium.com/@mccode/the-misunderstood-docker-tag-latest-af3babfd6375) for some background)

> Example: I'd also like the Docker image I'm working with to specify explicit versions of dependencies. [geoffreyd/ember-cli](https://registry.hub.docker.com/u/geoffreyd/ember-cli/dockerfile/) for example does not specify which node-version to use.

Explicit versions also give the benefit of more easily recreating the environment if needed, for example if the Docker image is removed, or a custom image is to be built with new features. With this in mind, I've chosen [danlynn/ember-cli](https://registry.hub.docker.com/u/danlynn/ember-cli/) as my Docker image for the day.

### Create application

I'll create a new directory, `mkdir hello-ember-again`, `cd hello-ember-again` and then ask ember-cli, from with inside a temporary Docker container, to generate my application, storing the files in my current directory.

```
docker run --rm -it -v $PWD:/myapp danlynn/ember-cli:0.2.7 init
```
Some notes:

* `--rm` tells Docker to delete the container afterwards. We aim to keep the containers ethereal.
* `-it` means interactive, with tty. In this case just to pretty-print the output from the command since we don't expect any need for keyboard input
* `-v $PWD:/myapp` maps the working directory in the container, where ember-cli will generate files, to the current directory on my host
* `init` will be passed as an argument to [ember-cli](http://www.ember-cli.com/user-guide/), as the [danlynn/ember-cli](https://registry.hub.docker.com/u/danlynn/ember-cli/dockerfile/) image has set ember-cli as the [entrypoint](https://docs.docker.com/reference/builder/#entrypoint) of the image

E.g. a slightly more convoluted way of running `ember init`, but inside the Docker container instead.

### Copy files
Since I this time will copy all files from my current directory (and I've noted that the Ember skeleton, with all dependencies, stacks up to 34.000+ files) I need to be more efficient in the initial copy of files to the container. After a bit of measuring, it seems the fastest way is to compress all files on the host and then uncompress in the container. I've also chosen to go back to the Docker volume feature. A quick test shows that a shared volume between host and container works well, as long as it isn't a directory being monitored by the container (Ember in this case).

##### Copy strategy
1. Compress all files in the current directory into a single file
1. Start Docker container with a shared volume between host and container (including the current directory)
1. As a start command in the container, unpack all files
1. If a file is changed on the host, run a command in the container to copy the file from the shared directory to the directory in use (by Ember)

##### Some details
* I use [gulp-tar](https://github.com/sindresorhus/gulp-tar) to compress files. Not as efficient as executing tar on the command-line but good enough, and at some time I'd like my gulpfile to be platform-independant
* For simplicity, I'm adding the project directory as a whole as a shared directory in the container.
* The Ember Docker container is now started with `tar -xmf /tmp/myapp/tmp/init.tar && ember server`, e.g. unpack files, then start ember
* To copy individual files that have been updated, I run `docker exec <container> cp /tmp/myapp/tmp/<file> /myapp/<file>`, e.g. run copy inside the container, from the shared directory to the app directory

### Run application

I'm still going with [Docker Compose](https://docs.docker.com/compose/), nothing much new from the last post apart from image choice, volume share and command to start. Note that since the image has an entrypoint set, I need to override it to run my special command.

```
# docker-compose.yml
ember:
  image: danlynn/ember-cli:0.2.7
  ports:
   - "80:4200"
   - "35729:35729"
  volumes:
   - .:/tmp/myapp
  entrypoint:
   - sh
  command: -c "tar -xmf /tmp/myapp/tmp/init.tar && ember server"
```

# Evaluation

* I can still run `gulp up`. Starting now takes 20 seconds (15 just to compress files..). Not ideal, but since I have live reloading in place I'm ok with that.
* File copying is simpler/faster than last time.
* I can use Ember commands, without having Ember installed
* Live reloading is working again (since we're using `ember server` to serve our page again)
* The gulpfile is still platform-dependent (using `docker` and `docker-compose` commands to start and update the environment).

### References
* [GitHub source](https://github.com/erikxiv/hello-ember-again)