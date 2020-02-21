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
    * use `git branch` or `git status` to make sure you are on the right branch


* It is good to have a remote host for your repository:

    * use web services like GitHub, GitLab, etc.


* It is a good practice (in most cases) to commit your work:

	* use **stashes** or **branches** to save your local changes

	* use `git checkout` only if you are absolutely sure you don't need local changes

	* Git commits can be reverted:

		* use `git revert <hash>` to undo the commit - this creates a new commit

	* Git commits can be modified:

		* use `git commit --amend` to modify the last commit (this changes history!)

	* Git commits can be removed:

		* use `git reset --hard <hash>` to bring HEAD to `<hash>` and remove all later commits (this changes history!)
		* use `git reset --hard @{u}` to make the local branch identical to upstream (this changes history!)

	* **Some Git commands rewrite history - try to avoid them on public commits**



## Links

* [Learn Git branching in an interactive way](https://learngitbranching.js.org/)
* [Another platform to play interactively with Git](http://git-school.github.io/visualizing-git/)
* [On undoing, fixing, or removing commits in git](http://sethrobertson.github.io/GitFixUm/fixup.html)
* Need a Cheat Sheet?
    * [Interactive git cheatsheet](http://www.ndpsoftware.com/git-cheatsheet.html)
	* [Detailed pdf](https://users.aalto.fi/~darstr1/cheatsheets/git-for-normal-people-cheatsheet_1.0.pdf)
	* [Cheat sheets in multiple languages](https://github.github.com/training-kit/) 

* Other links:

    * [https://git-scm.com/doc](https://git-scm.com/doc)
    
    * [http://sethrobertson.github.io/GitBestPractices/](http://sethrobertson.github.io/GitBestPractices/)
    * [https://try.github.io/](https://try.github.io/)
    * [http://gitready.com/](http://gitready.com/)
    * [http://git-school.github.io/visualizing-git/](http://git-school.github.io/visualizing-git/)
    * [http://documentup.com/skwp/git-workflows-book](http://documentup.com/skwp/git-workflows-book)
 
