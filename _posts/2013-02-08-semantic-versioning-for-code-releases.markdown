---
layout: post
title: "Semantic Versioning for code releases"
date: 2013-02-08 13:58
comments: true
categories: [semantic versioning]
---

Rake version got bumped to 10 straight from 0.9 recently. This is what the creator had to say about this:

{% blockquote Jim Weirich http://rake.rubyforge.org/doc/release_notes/rake-10_0_0_rdoc.html Rake 10 Release Notes %}
Due to historical reasons (or maybe just plain laziness) Rake has (incorrectly) been treating the second digit of the version as the major release number. So in my head Rake was already at version 9.
Well, it’s time to fix things. This next version of Rake drops old, crufty, backwards compatibility hacks such as top level constants, DSL methods defined in Object and numerous other features that are just no longer desired.
It’s also time to drop the leading zero from the version number as well and call this new version of rake what it really is: Version 10.
So, welcome to Rake 10.0!
{% endblockquote %}

If you're a developer, it's inevitable that you write API's that are consumed by others. It may be a RubyGem or a Rails app, or just a file that does something.
It's also inevitable that you continuously improve your code or add features to it. Versioning let's your API consumers know what's going on from a really high level.
You let them know whether they can just upgrade the code with your latest release or whether they should be concerned just by looking at the version.

This is what a version should look like:

`<major>.<minor>.<patch>`

* And these are the guidelines that determine which numbers should change in a release:

  1. *Breaking backward compatibility bumps the major (and resets the minor and patch to 0)*  
    Users should be aware of the breaking changes. A changelog file will be nice to have.
  2. *New additions without breaking backward compatibility bumps the minor (and resets the patch to 0)*  
    Users can upgrade without worrying and make use of the new features.
  3. *Bug fixes and misc changes bumps the patch*  
    Users should probably take these changes in whenever available. It may prevent a lurking bug from coming out into their system.

Refer [Semantic Versioning specs](http://semver.org/) for more info.
