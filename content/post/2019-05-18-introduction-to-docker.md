---
layout:     post
title:      "Introduction to Docker"
subtitle:   "Docker in 5 minutes"
date:       2019-05-18
author:     "Łukasz Chrząszcz"
description: "Grasp the idea of what Docker is and how it works. We'll setup basic container with Ubuntu and run some commands on it."
URL: "/2019/05/19/introduction-to-docker/"
image:      "/images/post-bg-2019-05-18-introduction-to-docker.png"
---

Docker! That’s a real buzzword today! If you haven’t had a chance to use it yet, then you probably wonder what problems does it solve and if it can come in handy for you? Have you ever struggled with a problem of installing new software just for <strong>trying something out</strong>? Doing this regularly can lead to chaos in your OS not to mention the extra hassle of configuring more complex or coupled software (Wordpress with <a href="https://pl.wikipedia.org/wiki/LAMP">LAMP</a>). Maybe you are the one who want to create <strong>easy to deploy software</strong>? Among others these are the reasons to consider <strong>Docker</strong>. In this post I’ll give a quick introduction to this tool so you can get a gist of an idea in one evening. Let’s get started!

What is a Docker? It is so popular that you probably know more or less what it is - anyway - according to documentation Docker is a <strong>platform for developing, shipping and running applications</strong>. So what’s the deal? Isolation! Docker uses features like <a href="https://en.wikipedia.org/wiki/Cgroups">cgroups</a>, <a href="https://en.wikipedia.org/wiki/Linux_namespaces">namespaces</a>, <a href="http://man7.org/linux/man-pages/man7/capabilities.7.html">capabilities</a> and other Linux kernel stuff to lock down your process in a “software cage” by limiting resources like CPU, memory as well as ability to see other things in the system like processes or files. If you’re interested go check those mechanisms, but I think you can imagine how Docker might work under the hood using them.

Before we go further be sure to have Docker installed - installation process is fairly simple and you can follow the instructions for Ubuntu <a href="https://docs.docker.com/engine/installation/linux/ubuntulinux/">here</a>.

# Image vs container

Two most important things in Docker world are image and container. You can thing of an <strong>image</strong> as a some kind of ISO image or CD disk with your favorite system installation. In particular, docker image contains all things that are required to create a running thing - <strong>container</strong>. Container is much like a living thing inside your OS, process, or something. You can start it, stop it, change it, save it.

# Hands on - cowsay on Ubuntu

I’ll show you a quick thing you can do with Docker - install random software and check it out. In our example it’ll be popular <strong>cowsay in Ubuntu environment</strong>.
Let’s get started with running Ubuntu container - remember you’re not installing a virtual machine with ubuntu, but creating <strong>“isolated box” with environment just like ubuntu</strong> (libraries, tools, binaries, whatever, etc.).

```bash
docker run -t -i ubuntu
```

It’ll take a while at the beginning - why? Docker checks if you have already downloaded Ubuntu images and if you don’t then downloads it from <a href="https://hub.docker.com/">repository</a>. If you happen to run ubuntu again later then it’ll be much quicker.

Anyway, you’ll end up in a prompt within your <strong>new ubuntu environment</strong>, you can fool around, install something - let’s install cowsay! Execute this command from within your container:

```bash
apt-get update
apt-get install cowsay
/usr/games/cowsay 'I like Nerdy Geek!'
```

Don’t leave your container just yet - open up a new terminal and execute:

```bash
docker ps
```

You’ll see your container running (see a funny name generated for it? :smile: Mine was ‘gigantic-panini’). This command give you a <strong>list of all running containers</strong>.

You can now hit Ctrl+C to leave your container. Actually you’ve just stopped it - execute ps command once again - it’s not there. You can fix it by adding –all option

```bash
docker ps --all
```

There - you should see your container. You probably think - oh my god this sucks! <strong>I want my cowsay back!</strong> Fear not! The thing is that you ran ubuntu in interactive mode in which container automatically stops after you leave shell (more or less). Let’s bring back your ubuntu to life. Replace <em>funny-name</em> with the name of your stopped container.


```bash
docker start <funny-name>
```

Ok, your ubuntu should be <strong>running in the background</strong> right now - check it with ps command. Now if you want to execute cowsay again you can do it easily by connecting to your container.


```bash
docker exec -ti <funny-name> bash
/usr/games/cowsay 'Actually I love NerdyGeek!'
```

When you’re done with your container you can stop it with:

```bash
docker stop <funny-name>
```

That’s it! You’ve just created <strong>your first Ubuntu container</strong>, customized it and stopped it - you can always start it again and add something more, test something else, etc.

The last thing missing is - <strong>what about image?</strong> Well Ubuntu image you’ve downloaded from Docker hub wasn’t changed. In fact Docker is “layer-oriented” as one might say. Image has some layers in it and by running and changing container you’re introducing brand new layer on top of it, which - let’s say - has a higher priority over the image ones. Every change you make in container is saved to this new layer. Question here is - <strong>can I persist this layer somehow for the future? Can I save my container to be an image and why would I want to do that?</strong> Of course you can - this is so called “commit” and you can do it like that:

```bash
docker commit <funny-name> nerdy-geek-ubuntu
```

What docker did is that it applied your changes (your new layer) to the image so that when you <strong>run container from newly created image</strong> then:

  * You already have cowsay installed
  * New layer is created just above your previous one
  * You can create many containers from your new images and for example install fortune on one of them and postgres (why not?) on the other one
  * You can commit those new containers and get 2 new images with cowsay and fortune as well as cowsay and postgres.
  * You can easily share your new super awesome image with others

# What more can Docker do?

What I described is just a small part of Docker functionality and I’ll for sure write more about it, but to give you an idea of <strong>what can be done</strong> you have to be aware of the following:

  * You can manage <strong>networks</strong> - a.k.a. put a few containers in common network, and others in other - isolated network
  * You can <strong>map ports</strong> from container to your host system (access postgres running within container from your colleague’s computer)
  * Manage <strong>data volumes</strong> - persistent storage which can be accessed by many containers - for example postgres data folder.
  * Create more advanced images with <strong>Dockerfile</strong> - i.e. instruct Docker to install cowsay while creating container so it’ll be up-to-date not the version when you created your image.
  * Much more!

# Summary

Docker is awesome and I’m pretty sure that you are now hyped about it as well! Go check the official documentation for more awesome things you can do. Soon I’ll write post about utilizing Docker in a development cycle and an example on running a real software like elasticsearch. Stay tuned and see you soon!
