---
author: erikxiv
categories: erikxiv
layout: post
cover: false
navigation: true
layout: post
title: Home Project Workflow
date: '2015-06-22 20:26:14'
subclass: post
tags:
- docker
- workflow
- travisci
- nodejs
- github
---

> Well, its this time of year. A combination of inspiration and energy to start new development projects and a frustration due to the ratio I spend at scaffolding versus the ideas themselves. What to do? Why not ponder the yearly renewal of my workflow and tool-set?

# Guidelines

* **Fast.** This is my free time and my memory isn't what it used to be. If the idea isn't done soon it is gone forever.
* **Timeless.** Next time inspiration hits, an updated tool-set or module versions should not break your stride.
* **Simple.** I want to force myself to think small (and doable). If it helps, Mozart is praised for the simplicity of his melodies.
* **Rich.** I want to be able to use the latest gadgets with as much default settings as possible.
* **Friendly.** Friends should be able to quickly drop in and out of the project to contribute and socialize.

# Development cycle

1. **Wait, why doesn't this exist already?.** Ponder great ideas, draw diagrams, sketch business plans. Not covered in this post.
2. **Run, Forrest, run.** Write code, design UIs, download the latest buzz, create and run unit-tests to verify that all party members are still behaving properly.
3. **Is it good enough yet?** Play around with the new features, perhaps invite a friend to give some valuable feedback.
4. **Awe the world.** Let it loose in the wild, sit back and enjoy a cup of tea while clapping yourself on the back.

### Run, Forrest, run
All right, the little grays have decided on a target for the next 4h cycle. Something is to be modified to make that happen, and in my line of projects that tend to be text. Glory comes in many forms.

##### Some ground rules:

* **Version control.** Not that I've ever rolled back changes in home-grown projects, but it is cheap, easy, a nice place to store the glory and simplifies in a lot of ways. I've long since grown out of the idea that my innovations are that innovative and must be kept secret and use [Github](github.com) or other public free services.
* **Fast recycling.** I find that home-projects for various reasons must be optimized for a short attention span. One development cycle should be estimated to about 4h from start to finish, or risk getting stuck in an incomplete state from where it is much less likely you'll start again when you get back to the task a few weeks later.
* **Modularization.** Dream big, build small. Divide your million-dollar ideas into smaller parts, where each part is an individual project, e.g. one repository in github, one test suite etc. Assemble into bigger pieces.

##### Text editing
Call me old-fashioned, or perhaps new-fashioned?, but I'm aiming for a text editor workflow.

* I am very fond of my copy of [Sublime Text](http://www.sublimetext.com/) in its *Simplicity*
* [Eclipse](https://eclipse.org/) and [Visual Studio](https://www.visualstudio.com/) seem to limit my options (think *Rich*) rather than aid my productivity. This is of course a big question of technology stack.
* All my *Friends* have a text editor

This, however, leads to higher requirements/focus on a scripted workflow.

##### Docker
The new kid on the block, but it offers a few things I want to try out.

* I can, at least in theory, start an entire eco-system of ready-made-by-others services with a single [Docker Compose](https://docs.docker.com/compose/) command.
* No need to install dependencies such as a specific database version on my current computer (or my *Friends*)
* Neat way to package other *services* when assembling a new application
* Should provide a simple deployment model for runtime environments

Docker in the development phase however comes with a drawback. I want to be able to view/test my changes directly after saving my latest text modifications. As my text editor is running on my host, that basically means I have to automate *deployment* of changes to the Docker image hosting the current code project.

Again, I'm pushing some complexity into the scripted workflow.

##### Workflow summary

1. Inspiration hits. Run to computer.
1. Open a terminal window, type `cd project && subl . && gulp up` to start the project environment
1. Make changes to a text file or two. Save.
1. View results, e.g. open web browser to app UI or unit-test report
1. Type `Ctrl-C`in the terminal window to bring the environment down
1. Commit to Github

**Note to self:** The development environment should NOT be used as a runtime environment; adding interesting data, playing around with  interactions and showing friends should be relegated to a stable *acceptance test* environment. The development environment should be a bare minimum environment, containing stable test data, test cases, mocked services where possible. All in the aim of **Fast** and **Simple**.

##### Friendly requirements
The following requirements *should be all that is required* of friends before jumping in.

1. [Install Docker](https://docs.docker.com/installation/)
1. [Install node](https://nodejs.org/) and [gulp](http://gulpjs.com/) (we need a bootstrap that is hip and platform-independent)
1. Check out project from GitHub

### Is it good enough yet?
It might be overkill, and doubly so in case of module/library projects, but when it comes to "customer-facing" applications I want a just-as-production-but-with-simpler-data environment. Also an excellent place to run automated integration and system tests.

This essentially means an identical setup of whatever cloud services I'm using in production.

##### Plan

1. Commit changes to [GitHub](http://github.com) (see previous chapter)
1. [Travis CI](https://travis-ci.org/) kicks in
  1. Builds, packages, beautifies
  1. Runs automated unit-tests (fails build if necessary)
  1. Deploys to *acceptance test* environment
  1. Runs automated ui and integration tests with a nice report being published somewhere

Wait, what? That's right, I'm busy with a cup of coffee, remember?

### Awe the world
One click should always be enough. Ask [Travis CI](https://travis-ci.org/) to deploy the the *production* environment.

# What now?
Right. Time to roll up my sleeves and see if I can make this work.