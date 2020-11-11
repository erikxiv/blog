---
author: erikxiv
categories: erikxiv
layout: post
cover: false
navigation: true
subclass: post
title: Docker on Synology
date: '2015-06-20 19:40:24'
tags:
- docker
- synology
- reverse-proxy
- apache
---

> I just found out that my NAS, a Synology DS412+, can host docker containers. It just went from a simple file storage and backup solution to a toy for proof-of-concepts. Next question: what will the capacity limit be?

## Reverse proxy

To avoid the need to open a million ports to the Internet, not to mention the user-friendlyness of using standard port 80, I want a reverse proxy for all dockered services. Any service receives its own DNS alias to separate them and internally these are routed to individual docker containers.

1. I would prefer to use https://registry.hub.docker.com/u/jwilder/nginx-proxy/ to automate settings as much as possible. After an hour or two I can't seem to find a way to connect the local /var/run/docker.sock to the container though. Google gives no clear way to solve the issue now.
2. Second option, a reverse proxy running in a docker container. Options galore of choices such as HAProxy, nginx containers etc with various sweeteners to make my life easier. Two hours later though, still can't seem to get Synology to free up port 80 to route this to a docker container. Most likely a hard-to-find setting somewhere locking this port to Web Station et al and not allowing its use for the docker package.
3. Third option, doable although not as pretty. Use the built in Apache-server and use it as the reverse proxy. Requires manual work to add new services (add a line in a script) but what the hell. Used  the init-script from https://primalcortex.wordpress.com/2014/03/14/synology-dsm-5-web-station-and-virtual-sites-for-filestation/ as a base.

## Next steps

* Well, I got a new blog space (http://ghost.org/) running as a docker image
* Planning on doing a ready-to-use proof-of-concept work-flow. Perhaps a Jenkins setup? Still like to use cloud-services as much as possible, even though the free price-tag for certain services could be beneficial for most of the "million-dollar-projects-in-a-blue-moon" I seem to do.