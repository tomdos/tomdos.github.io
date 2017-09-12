---
layout: post
title: "Deleting github history"
date: 2017-09-12
published: true
tags: git github rebase squash
---

From time to time one may need to delete history in certain github branch. Whenever I
find myself in such situation I use `git rebase` and its `squash` which do the
job in most of the time. However disadvantage of rebasing is you need at least
one commit. This was exactly the reason why I could not use `squash` this time.

Recently, we were publishing one of our private repo and we wanted to be sure that all previously
hardcoded passwords/keys stay uncompromised. The solution seems to be creating a new
orphaned branch with a single commit and then replacing master branch with the new one.

Create new branch called `my_new_master`.
```
git checkout --orphan my_new_master
```

As the branch is orphaned it doesn't contain any previous commits thus any files. We
need to add all files into it.
```
git add -A
```

Create an initial commit.
```
git commit -m "Initial commit"
```

We need to replace "old" master now which means we have to delete it at first.
```
git branch -D master
```

Rename the current branch `my_new_master` to `master`.
```
git branch -m master
```

Rewrite old remote master with the new one.
```
git push -f origin master
```
