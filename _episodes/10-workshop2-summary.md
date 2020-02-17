---
layout: episode
title: Workshop 2 - summary
teaching: 0
exercises: 0
objectives:
  - reviewing key points of Git - workshop 2
---

## What we have discussed so far:

* Git workflow with branches - useful tips:

    * use branches to experiment and to work on things in parallel;
    * branch often
    * keep the `master` branch as clean as possible
    * make sure you are on a right branch (`git branch`, `git status`)


* It is good to have a remote host for your repository:
    * e.g. use web services like GitHub, GitLab, etc.


* It is a good practice (in most cases) to commit your work:

	* use **stashes** or **branches** to save your local changes

	* use `git checkout` only if you are absolutely sure you don't need local changes

	* Git commits can be reverted:

		* use `git revert <hash>` to undo the commit - this creates a new commit
		* use `git reset --hard <hash>` to bring HEAD to <hash> 
		* use `git reset --hard @{u}` to make the local branch identical to upstream 

	* Some Git commands rewrite history:

		* try to avoid them on public commits
		* 



## Links

* [https://git-scm.com/doc](https://git-scm.com/doc)

* [http://sethrobertson.github.io/GitBestPractices/](http://sethrobertson.github.io/GitBestPractices/)
* [https://try.github.io/](https://try.github.io/)
* [http://gitready.com/](http://gitready.com/)
* [http://git-school.github.io/visualizing-git/](http://git-school.github.io/visualizing-git/)
* [http://documentup.com/skwp/git-workflows-book](http://documentup.com/skwp/git-workflows-book)
 
