---
layout: post
title: "Push local master to a different 'feature' remote branch"
date: 2013-03-05 19:58
comments: true
categories: [git]
---

You work on an independent POC as part of your project,
so you have a fresh directory with your POC code which has a local git repo.
Now you are asked to push this code to a fresh branch on their remote repo.

So this is what you have : a local master branch. This is what you want to achieve : 

1. You want to `git pull` from remote/feature and merge on local/master
2. You want to `git push` from local/master and push to remote/feature branch

You want a clean remote branch called `feature` without any history.

####Clone the remote repo in a different directory and create an orphan branch :
    git clone <remote repo url>
    git checkout --orphan feature
    git rm -rf .


####Push `feature` branch :
    echo "Feature branch" > README.md
    git add .
    git commit -a -m "Feature branch created"
    git push origin feature

Now you have the `feature` branch in your remote which is an orphan.

####Add remote in your local dev repo:
    git remote add origin <remote repo url>
    git fetch
    git remote set-url --push origin <remote repo url>
    git config remote.origin.push refs/heads/master:feature
    git branch --set-upstream master origin/feature

####Done
    git pull
    git push

