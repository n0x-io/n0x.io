---
title: "GIT_IN_MINUTES"
date: 2020-07-02
draft: false
---

# A Git-Server Setup In Minutes

Sometimes I wish I had other hobbies then just trying out new stuff I dicovered online. 

For Some strange reason I felt the need to set up my own git server - because who doesn't need an own server for the two or three projects you keep on github?
I looked around and searched for some solutions that would fit my needs.

My requirements were:
* Easy setup
* Very Low System requirements for the host
* Account management 
* Private and Public Repos
* Open Source

## The Possibilities
There were multiple solutions to choose from. 

The first one was [GitLab Core](https://about.gitlab.com/install/). [GitLab](https://about.gitlab.com) is widely know and a nice alternative to [GitHub](https://github.com/). But GitLab core does require some more System resources then my current Server has and so I ruled it out. It is good for bigger companies but I just needed something small.

[Gitea](https://gitea.io/en-us/) was the next thing I came across. It checks all the boxes: lightweight, simple install, Open Source, etc. I downloaded it but had trouble getting it to run with my current database without exposing my database port to the internet and that is a big No. I don't want to open any more ports in my firewall then I need to.

Here is where [GitBucket](https://gitbucket.github.io/) (not to be confused with ATLASSIANs [Bitbucket](https://bitbucket.org/)) came into play. It has similar features as Gitea but I was able to set it up exactly how I wanted to.

So now [git.n0x.io](https://git.n0x.io/) is a thing!

## Setting up GitBucket

### Prerequirements 

Just FYI I am running a little Debian 9 vServer so you might need to adapt the commands and changes to your system - which should be self-explanatory ;)

GitBucket is mostly written in Scala so we firstly need to install openJDK on our Server to run it.

    sudo apt update
    sudo apt install openjdk-11-jre

After installing the JRE I created a user account on which GitBucket should run. This way we make sure that I we do not grant to much privileges to GitBucket - it only gets the minimal things it needs to run

    sudo adduser --system gitbucket

The `--system` parameter creates a systemuser account which mean a user that has no shell so we can't log into it the normal `su` way. To login to the new user use the following command:

    sudo su - gitbucket -s /bin/bash

With `-s /bin/bash` we provide a shell to the user so we don't get logged out immediately.

Now we download the [latest version](https://github.com/gitbucket/gitbucket/releases/) of BitBucket (in my case 4.32.0) from GitHub to our local home.

    cd ~
    wget https://github.com/gitbucket/gitbucket/releases/download/4.32.0/gitbucket.war

### Running GitBucket

That is pretty much it. Now we can run GitBucket.war for the first time. There are multiple parameters you can specify ([See the official installation guide](https://github.com/gitbucket/gitbucket#installation)) but for now we stick with just specifying the port we want GitBucket to run on initially

    java -jar gitbucket.war --port=8080

If you have a firewall set up like I do, you of course need to open up the port you specified. For example if you use iptables:

    sudo iptables -A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT
