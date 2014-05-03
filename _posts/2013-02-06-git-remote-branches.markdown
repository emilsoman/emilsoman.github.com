---
layout: post
title: "Git Remote Branches"
date: 2013-02-06 17:16
comments: true
categories: [git]
---

To avoid forgetting and depending on random links from google again :

####Push a local branch called `new_branch` to a remote branch :
    git push -u origin new_branch

####To checkout a branch `new_branch` from remote `origin/new_branch`:
    git checkout -b new_branch origin/new_branch
Or:
    git checkout --track origin/new_branch

####To delete a remote branch `serverfix`:
    git push origin :serverfix

####To delete all stale(already deleted in remote) branches :
    git remote prune origin
    #add --dry-run to see a list of branches that will be pruned without actually doing it.
    git branch -d branch_to_be_deleted
