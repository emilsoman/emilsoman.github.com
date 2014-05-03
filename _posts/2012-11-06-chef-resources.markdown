---
layout: post
title: "Chef Resources"
date: 2012-11-06 18:09
comments: true
categories: [chef, resources]
---

Here's a list of all the Resources that Chef can work with :

1. **Cookbook File** - Transfers files in `files/` directory of a cookbook to the node
2. **Cron** - Manages cron entries
3. **Deploy** - Deploys your app, talks to the project's source control
4. **Directory** - Manages a directory
5. **Env** - This is for windows to set the environment
6. **Erlang Call** - Connects to a distributed erlang node and makes a call
7. **Execute** - Executes a command
8. **File** - Manages files already on the node
9. **Git** - Source Control Management
10. **Group** - Manages a local group
11. **HTTP Request** - Send an HTTP request with an arbitrary message
12. **Ifconfig** - Manage network interfaces
13. **Links** - Manage symbolic/hard links
14. **Log** - Adds log entries from your recipes
15. **Mdadm** - Manage [mdadm](http://en.wikipedia.org/wiki/Mdadm) software RAID devices
16. **Mount** - Manage a mounted filesystem
17. **Ohai** - Reload node's ohai configuration. Recipes can use it to reload the configs after touching any of those.
18. **Package** - Install a package (*gem_package - to install gems* )
19. **PowerShell Script** - Executes a script using the PowerShell interpreter
20. **Remote Directory** - Recursively transfers directories from `files/default` directory of cookbook to the node
21. **Remote File** - Transfer files from a remote source
22. **Route** - Manages system routing tables. Works only on Linux now.
23. **Ruby Block** - Executes ruby code when the recipes run , not during compilation
24. **SCM** - Source code management
25. **Script** - Execute a script using the specified interpreter
26. **Service** - Manage a service
27. **Subversion** - A provider for the SCM resource
28. **Template** - Manage file content with Ruby ERB template
29. **User** - Manage local users

For examples and detailed documentation , here's the [official doc](http://wiki.opscode.com/display/chef/Resources) .
Now you know what resources to use when writing your own recipes !
