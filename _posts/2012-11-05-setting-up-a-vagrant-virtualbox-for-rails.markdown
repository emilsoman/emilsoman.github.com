---
layout: post
title: "Setting up a Vagrant VirtualBox"
date: 2012-11-05 17:04
comments: true
categories: [vagrant]
---

For all the experimentation that I'll be doing for the next couple of weeks in my current project , I thought it'd be nice to have a virtual box set up.
I'm choosing [Vagrant](http://vagrantup.com/) for this.

So this is what I did.

Since I'm using Ubuntu 12.04 32-bit, I downloaded [that version](https://www.virtualbox.org/wiki/Linux_Downloads) of Oracle VirtualBox - very straightforward.

Then , installed Vagrant gem :

    gem install vagrant

Then I went to my project folder which I want to be shared with the VirtualBox and ran this :

    vagrant init lucid32 http://files.vagrantup.com/lucid32.box
    vagrant up

This downloads the lucid32 box from the URL. This takes a while. Once that's done , you can do :
    
    vagrant ssh

And you're in the VirtualBox . Pretty neat !

***Note** : You need to do a little bit more than that to get a complete Rails development environment set up. Follow [this excellent blog post](http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/), to get there.*
