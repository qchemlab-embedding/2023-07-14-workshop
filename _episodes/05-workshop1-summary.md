---
layout: episode
title: Workshop 1 - summary
teaching: 0
exercises: 0
objectives:
  - reviewing key points of Git - workshop 1
---


## What we have discussed so far:

* "Initializing a Git repository is simple: `git init`"

* use `git status` **a lot**

* use `git add` - `git checkout` - `git commit` workflow:

    * `git add` every change that improved things.
    * `git checkout` every change that made things worse.
    * `git commit` as soon as you have created "a nice self-contained unit".

* this workflow creates a story (find a balance between saving often and commiting)

* take care of writing informative `git commit` messages

* maintain clean working area:
    * use .gitignore for untracked files
    * "All files should be either tracked or ignored"

* use branches to experiment and to work on things in parallel; then if you do:
    * branch often
    * keep the `master` branch as clean as possible
    * always make sure you work on a branch you want to work on 
      (use `git branch` or `git status`)

* it is good to have a remote host for your repository:
    * e.g. use web services like GitHub, GitLab, etc.


## Links

* [https://git-scm.com/doc](https://git-scm.com/doc)

* [http://sethrobertson.github.io/GitBestPractices/](http://sethrobertson.github.io/GitBestPractices/)
* [https://try.github.io/](https://try.github.io/)
* [http://gitready.com/](http://gitready.com/)
* [http://git-school.github.io/visualizing-git/](http://git-school.github.io/visualizing-git/)
* [http://documentup.com/skwp/git-workflows-book](http://documentup.com/skwp/git-workflows-book)
 
