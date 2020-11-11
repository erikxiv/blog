---
author: erikxiv
categories: erikxiv
layout: post
cover: false
navigation: true
layout: post
title: Neo4j on Synology
date: '2015-06-20 20:01:26'
subclass: post
tags:
- docker
- synology
- apache
- neo4j
---

> As [previously mentioned](/docker-on-synology) I seem to be able to host docker containers on my Synology NAS. Although preferring pure cloud services for my projects, there is a case to be made for the free hosting service of my NAS when there is no free hosting version of the service I want. Especially when it calculating the income-to-cost ratio of my standard million-dollar-ideas.

#Neo4j

I've recently woken up to the benefits of Neo4j as a graph database. I still favor the theoretical framework, and the standardization efforts of the RDF space, but when time is limited it is really rewarding to use a ready-to-use tool that gets you started in hours rather than weeks as you don't need to build the surrounding tooling yourself.

Neo4j is provided in a nice [start-immediately docker container](https://registry.hub.docker.com/u/tpires/neo4j/). I do however want to use a non-standard port behind a reverse proxy for two reasons: primarily I'm likely to want more than one neo4j database, and I like to avoid the hassle of maintaining a list of ports for various services under development. ~~For some reason, neo4j requires some special handling to use a non-standard port, but there are a nice guide on how to modify the requests/responses on the fly with Apache as a reverse proxy: http://nicholaspaulsmith.com/running-neo4j-admin-console-behind-a-reverse-proxy/ (one may skip the authentication part if that is not interesting). I had to add ``LoadModule substitute_module modules/mod_substitute.so``to /etc/httpd/httpd.conf~~

Update: After [RTFM](http://neo4j.com/docs/stable/server-configuration.html), it seems neo4j wants two HTTP headers to be set  when behind a reverse proxy, ``X-Forwarded-Host``, which is set automatically by Apache and ``X-Forwarded-Proto``, which I had to add the following line to the Apache proxy configuration: ``RequestHeader set X-Forwarded-Proto "http"``. It does however only  seem to affect protocol and host; Neo4j still returns the incorrect port in redirects and data links. Still works, but..