---
layout: post
title: "Roll Your Own Gems with Jeweler"
date: 2012-11-07 21:15
comments: true
categories: [jeweler, gems]
---

I use [jeweler](http://github.com/technicalpickles/jeweler) for my gems. The use of gems 
like jeweler or hoe for gem development is a much debated topic, but it was pretty easy maintaining 
gems with jeweler.

Here's a simple demo .

First install jeweler :
    gem install jeweler


It's very important to print "Hello World" in many projects. So we'll make a gem for that.

First, you'll have to create a repo on GitHub with the gem name, here, 'hello_world'. Once that's done, 
we'll use jeweler to create a skeleton of the gem.

    jeweler hello_world --rspec --github-username emilsoman --github-token any_random_string

This will create the gem directory `hello_world` where your gem lives. You can also see that 
the directory is now a git repo and the code has been pushed to GitHub.

    git remove -v
    origingit@github.com:emilsoman/contractor.git (fetch)
    origingit@githubithub.com:emilsoman/contractor.git (push)
    
*__--github-token any_random_string__ is not used anymore, but jeweler still requires it as a mandatory option. This is a [bug in jeweler](https://github.com/technicalpickles/jeweler/issues/230). So we'll just provide some random string there.*

__UPDATE:__ I've fixed this issue in [my fork of jeweler](https://github.com/emilsoman/jeweler). You do not
have to provide the random github token if you use this fix.

Next step is to add some details to the gem. Open up Rakefile where jeweler keeps the details that would be placed later in hello_world.gemspec. Add a `summary` and `description` for the gem.

Open up Gemfile, remove the spermy operator for rspec and bundler, we can use the latest versions.
{% gist 4033896 Gemfile.diff%}
Also , I'm not using rcov, so I'm removing that gem from the Gemfile. 
Run `bundle install` to resolve dependencies.

Now we're ready to write the gem. Notice the `lib/hello_world.rb` file ? That's the file that 
gets _required_ when you require the gem using `require 'hello_world'`. So that's where we'll put this code :
{% gist 4033896 hello_world.rb %}

Now stage the changes. Write a version file, and release the gem. You need an account with RubyGems.org to release the gem there.
    git add .
    rake version:write
    rake release

Our super important hello_world gem is official now and ready to be used by millions across the world.

The gem release workflow using jeweler would be typically as follows:

1. Bump version using rake version:bump:<SEMANTIC VERSIONING>. Refer [this spec](http://www.semver.org) for more info on versioning.
2. Make all your changes and commit to repo. Update CHANGELOG for all the changes
3. Repeat step 2 till release is ready.
4. Before release, review changes and version
5. Run `rake release`

By the way , jeweler can do a few more things than that. Run `rake -T` inside the gem directory to see everything jeweler can do.
