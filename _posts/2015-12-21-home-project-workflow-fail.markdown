---
author: erikxiv
categories: erikxiv
layout: post
cover: false
navigation: true
layout: post
title: 'Home Project Workflow #fail'
date: '2015-12-21 00:29:37'
subclass: post
tags:
- docker
- workflow
- fail
---

> A colleague once told me that it is inherently difficult to be a good software architect consultant, as the contracts seldom extends long enough to analyze the outcome of the tough architectural choices made in the design and build phases of the project. This makes it difficult to become better, as you seldom get to see if your choices were good or bad.

This blog post has nothing to do with software architecture, but the quote comes to mind when I, five months later, on a different computer, try to boot the home project structure I blogged about earlier. Proud as always, I hoped that the structure would stand the test of time.

Remember the guidelines and workflow I posted in my [earlier post](http://blog.erikxiv.com/home-project-workflow/)?
> Timeless. Next time inspiration hits, an updated tool-set or module versions should not break your stride.

> ...

> 1. Inspiration hits. Run to computer.
> 2. Open a terminal window, type cd project && subl . && gulp up to start the project environment
> 3. ...

## What happened?

* Inspiration, check
* Run to computer, check
* Yada, yada, **gulp up**
  * ...Nice, long, stack trace error[^1]

Right - **boot2docker**, that I hardcoded into my gulp script, is no more, we now call it **docker-machine**. (...open `gulpfile.js`, search/replace boot2docker to docker-machine...)

* **gulp up**!
  * ..Nice, long, stack trace error[^2]

Right - `docker-machine env` behaves slightly different than `boot2docker shellinit`, adding quotes to environment variable output. (...open `gulpfile.js`, remove quotes before setting docker environment variables...) 

* **gulp up** (pretty, please?)
  * ..Nice, long stack trace error[^3]

Right - I seems to have forgotten to script installation of dependencies into the docker image. (...open `docker-compose.yml`, add `npm install && bower install --allow-root` to the start command...) 

* **gulp up** (yawn, getting late)

Success = Time to go to bed (inspiration forgotten)

### For those so inclined

[^1]: Stack trace of old executable
```
computer:helloworld user$ gulp up
[01:22:32] Using gulpfile ~/workspace/helloworld/gulpfile.js
[01:22:32] Starting 'tar'...
[01:22:32] Starting 'boot2docker-up'...
[01:22:32] 'boot2docker-up' errored after 5.64 ms
[01:22:32] TypeError: Cannot read property 'toString' of null
    at Gulp.<anonymous> (/Users/user/workspace/helloworld/gulpfile.js:82:45)
    at module.exports (/Users/user/workspace/helloworld/node_modules/gulp/node_modules/orchestrator/lib/runTask.js:34:7)
    at Gulp.Orchestrator._runTask (/Users/user/workspace/helloworld/node_modules/gulp/node_modules/orchestrator/index.js:273:3)
    at Gulp.Orchestrator._runStep (/Users/user/workspace/helloworld/node_modules/gulp/node_modules/orchestrator/index.js:214:10)
    at Gulp.Orchestrator.start (/Users/user/workspace/helloworld/node_modules/gulp/node_modules/orchestrator/index.js:134:8)
    at /usr/local/lib/node_modules/gulp/bin/gulp.js:129:20
    at doNTCallback0 (node.js:419:9)
    at process._tickCallback (node.js:348:13)
    at Function.Module.runMain (module.js:469:11)
    at startup (node.js:136:18)
```

[^2]: Stack trace of environment variable issue
```
Traceback (most recent call last):
  File "<string>", line 3, in <module>
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.main", line 39, in main
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.docopt_command", line 21, in sys_dispatch
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.command", line 27, in dispatch
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.docopt_command", line 24, in dispatch
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.command", line 57, in perform_command
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.command", line 79, in get_project
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.command", line 62, in get_client
  File "/compose/build/docker-compose/out00-PYZ.pyz/compose.cli.docker_client", line 33, in docker_client
  File "/compose/build/docker-compose/out00-PYZ.pyz/docker.tls", line 46, in __init__
docker.errors.TLSParameterError: Path to a certificate and key files must be provided through the client_config param. TLS configurations should map the Docker CLI client configurations. See http://docs.docker.com/examples/https/ for API details.
```

[^3]: Stack trace of missing dependencies issue
```
Pulling ember (danlynn/ember-cli:0.2.7)...
0.2.7: Pulling from danlynn/ember-cli
...
Digest: sha256:3ecbc1cb691fb31e0c8724eafd511caa4514f76763d79479051e0dbf36d8b486
Status: Downloaded newer image for danlynn/ember-cli:0.2.7
Creating helloworld_ember_1...
Attaching to helloworld_ember_1
ember_1 | 
ember_1 | Missing bower packages: 
...
ember_1 | Run `bower install` to install missing dependencies.
ember_1 | 
helloworld_ember_1 exited with code 1
```