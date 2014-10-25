---
layout: post
title: "Setting up Chef Client"
date: 2012-11-06 10:38
comments: true
categories: [chef, client]
---

We've set up a chef server in the [previous post](/blog/2012/11/05/hello/). That was on the host.
*Remember , we're planning to host the client on a Vagrant virtualbox and the server on the development box*

We need an `/etc/chef` directory to store the configurations for the client. We put `client.rb` and `validation.pem` in there :
  
*on the node , ie on the Vagrant box*
    sudo mkdir -p /etc/chef

To create the client.rb and validation.pem , you can do one of the two steps :

### 1. With Workstation

  ```bash
  #On Workstation
  ~> knife configure client ./
  ```

Now scp the client.rb and validation.pem files generated at the current working directory of the node.

### 2. Without Workstation

Create a barebone `/etc/chef/client.rb` directly on the client :

```ruby
#/etc/chef/client.rb on Node
log_level        :info
log_location     STDOUT
chef_server_url  'http://yourchefserver.com:4000'
validation_client_name 'chef-validator'
```

At a minimum client.rb must contain the configuration settings necessary for chef-client to communicate with the chef-server.

Also, download the `validation.pem` file from chef-server to `/etc/chef/` on the node

On the node , run :
    sudo chef-client -i 3600
This will register the node with the server , and create `client.pem` key in `/etc/chef/` directory in the node.
And starts the chef-client which will poll the chef-server every 3600 seconds for changes.

On the server, run : 
    knife node list
This should list your newly added client. If it does , congratulations! Your chef client is up and running .

Here's [another document](http://ceph.com/docs/master/install/chef/) that's helpful in getting chef server and client set up.
