---
layout: episode
title: Workshop 3 - summary
teaching: 0
exercises: 0
objectives:
  - reviewing key points of Git - workshop 3
---

## What we have discussed in workshop 3:


* Resolving conflicts:

	* manually:
		* look for resolution markers (the `<<<<<<<`, `>>>>>>>`, and `=======`) in unmerged files
		* decide what you keep and what you remove
		* stage (`git add`) and commit (`git commit`) that change

	* use a visual tool (`git mergetool`)

	* if you are not sure how to proceed:
		* abort the merge (`git merge --abort`)
		
    * choose one strategy:
		* maybe you want all changes from your branch (`git merge -s recursive -Xours`)
		* maybe you want all changes from the branch you are trying to merge (`git merge -s recursive -Xtheirs`)

	* conflicts can be avoided - choose your Git branch strategy wisely


* Git for travelling in time:

	* typical workflow:

		* use `git grep`, `git show`, 'git annotate` to identify a commit to inspect
		* do `git checkout -b branchname somehash` to branch off from that commit

	* if you are not sure which commit to inspect: 
		* find a commit where things were OK and a commit where things broke
		* use `git bisect`
 

* Advanced - more on working with branches:

	* `git merge` vs `git rebase`:
		* use `git merge` to keep a set of commits clearly grouped together in history
		* use `git rebase` when you want to keep linear commit history (**this changes history!**)
		* **do not rebase on shared repositories**
		* `git rebase -i` may be handy when you need to squash commits

	* Think of good branch hygiene, develop a good branching model for your project.

	* Excellent links:
		* [A successful Git branching model (especially for developers)](https://nvie.com/posts/a-successful-git-branching-model/)




## Links

* [https://git-scm.com/doc](https://git-scm.com/doc)

* [http://sethrobertson.github.io/GitBestPractices/](http://sethrobertson.github.io/GitBestPractices/)
* [https://try.github.io/](https://try.github.io/)
* [http://gitready.com/](http://gitready.com/)
* [http://git-school.github.io/visualizing-git/](http://git-school.github.io/visualizing-git/)
* [http://documentup.com/skwp/git-workflows-book](http://documentup.com/skwp/git-workflows-book)
 
