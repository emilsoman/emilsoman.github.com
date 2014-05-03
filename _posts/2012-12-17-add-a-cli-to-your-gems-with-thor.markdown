---
layout: post
title: "Add a CLI to your gems with Thor"
date: 2012-12-17 12:22
comments: true
categories: [thor, cli, gems]
---

I'm writing this ruby gem [homer](https://github.com/emilsoman/homer) which is basically a command line
tool which helps you to manage your dotfiles. When writing a CLI tool, all those repetitive tasks try to
drag you down : options parsing, help text, default options, shortnames for options etc.

[Thor](https://github.com/wycats/thor) is a rubygem whose aim is to take away this pain.

To create my homer executable file, this is what I did :

* Added a `bin/homer` file in my homer gem.
* Included the ruby shebang line :
      #!/usr/bin/env ruby
* Required "thor"
      require 'thor'
* Defined the Thor class
  {% highlight ruby %}
  class HomerRunner < Thor
    desc "init", "Unleashes homer to feed on your dotfiles"
    def init
      puts "Homer Unleashed !"
    end
  end
  HomerRunner.start
  {% endhighlight %}

That's really it! `bin/homer` will be an executable when the homer gem is installed and I can run `homer init`.

Now homer is one gem which will support a few subcommands and I want a help command to help confused users. Guess
what, thor took care of that already. `homer --help` is at your disposal. How about making `help` the default task?
Simple.
    {% highlight ruby %}
    class HomerRunner < Thor
      default_task :help
      ...
    end
    {% endhighlight %}
