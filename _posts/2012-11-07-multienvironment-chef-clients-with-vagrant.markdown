---
layout: post
title: "MultiEnvironment chef-clients with Vagrant"
date: 2012-11-07 13:55
comments: true
categories: [vagrant, chef]
---

I'm doing a lot of things with Chef in my current project, and I was looking for a way to test the client-server architecture of Chef . I could get hold of a couple of unused machines from the IT guys , but that isn't such a cool thing to do.

How about this - multiple environments in my own development machine, which I can screw up to my heart's content , erase everything and restart my test as many times as I want in a flash ? That would be sweet !

Say hello to Vagrant . I have Vagrant set up in my machine , and you should have it too . If you haven't done that already , [this post](/blog/2012/11/05/setting-up-a-vagrant-virtualbox-for-rails/) will help you do just that.

Enough chit chat , let's figure out the multi-environment thingy .

I want two independent virtualmachines, one running lucid32 another running precise32 , just for the demo. I’m going to use these as my chef-clients to test out Chef.

I destroyed my existing VM by running :
    vagrant destroy

Modified my Vagrantfile to look like this:
{% gist 4030169 Vagrantfile %}

lucid32 machine is *node1* and precise32 is *node2*

In one tab, I run :
    vagrant up node1
    vagrant ssh node1
    vagrant@lucid32:~$

and in a second tab :
    vagrant up node2
    vagrant ssh node2
    vagrant@precise32:~$

Follow [my post on setting up Chef-Client](/blog/2012/11/06/setting-up-chef-client/) to set up chef-client on the machines and we're done !
