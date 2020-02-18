---
layout: episode
title: Undoing things
teaching: 5
exercises: 5
questions:
  - How can I undo things?
objectives:
  - Learn to undo changes safely
  - See when undone changes are permanently deleted and when they can be retrieved
---

## Undoing things

- Commits that are part of any branch will not get lost.
- Files which were added and later removed can always be recovered.
- In Git we can modify, reorder, squash, and remove commits and also these actions can be undone.
- Some commands can permanently delete **uncommitted** changes. In doubt always commit first.
- Some commands **modify history**. This is OK for local commits but may not be OK for commits shared
  with others.

---

> #### Clear your workspace
>
> - If you have unstaged changes from earlier sections, remove them with `git checkout <filename>`.
> - We will see in more detail below how `git checkout` works.
>
{: .callout}


We start with the following state:

![]({{ site.baseurl }}/img/gitink/git-deleted-branches.svg)

  - what do we want to undo?

---

### Reverting commits

Let's make a new commit on a `master` branch (e.g. modify a README.md file):

```
$ git log --oneline

e1d7745 (HEAD -> master) testing a new idea
5861578 Merge branch 'less-salt'
0996fff Merge branch 'experiment'
372c868 add README.md file
721d9c6 reduce amount of salt
a85267e maybe little bit less cilantro
c79bfc1 let us try with some cilantro
901f422 enjoy your dish!
7adfe4b add half an onion
49baa1f adding ingredients and instructions
```

We realize that this commit (`e1d7745`) was a mistake and we wish to undo it

A safe way to undo the commit is to revert the commit with `git revert`:

```
$ git revert e1d7745
```

This creates a **new commit** that does the opposite of the reverted commit.
The old commit remains in the history:

```
$ git log --oneline

646c9e1 (HEAD -> master) Revert "testing a new idea"
e1d7745 testing a new idea
5861578 Merge branch 'less-salt'
0996fff Merge branch 'experiment'
372c868 add README.md file
721d9c6 reduce amount of salt
a85267e maybe little bit less cilantro
c79bfc1 let us try with some cilantro
901f422 enjoy your dish!
7adfe4b add half an onion
49baa1f adding ingredients and instructions
```

You can revert any commit, no matter how old it is.  It doesn't affect
other commits you have done since then - but if they touch the same
code, you may get a conflict (which we'll learn about later).

> ## Exercise: Revert a commit
>
> - Create a commit.
> - Revert the commit with `git revert`.
> - Inspect the history with `git log --oneline`.
> - Now try `git show` on both the reverted and the newly created commit.
{: .challenge}

---

### Adding to the previous commit

Sometimes we commit but realize we forgot something.
We can amend to the last commit:

```shell
$ git commit --amend
```

This can also be used to modify the last commit message.

Note that this **will change the commit hash**. This command **modifies the history**.
This means that we never use this command on commits that we have shared with others.

> ## Exercise: Modify a previous commit
>
> 1. Make an incomplete change to the recipe or a typo in your change, `git add` and `git commit` the incomplete/unsatisfactory change.
> 2. Inspect the unsatisfactory but committed change with `git show`.
> 3. Now complete/fix the change but instead of creating a new commit, add to the previous commit with `git commit --amend`.
{: .challenge}

---

### Bring your project back to a specific commit

#### With `git checkout`

*  `git checkout <hash>` checks out a specific commit and puts it in your work tree

> ## Exercise: create a new branch from an old commit
>
> Let's inspect our current state:
>
> ```
> $ git log --oneline
> 
> 646c9e1 (HEAD -> master) Revert "testing a new idea"
> e1d7745 testing a new idea
> 5861578 Merge branch 'less-salt'
> 0996fff Merge branch 'experiment'
> 372c868 add README.md file
> 721d9c6 reduce amount of salt
> a85267e maybe little bit less cilantro
> c79bfc1 let us try with some cilantro
> 901f422 enjoy your dish!
> 7adfe4b add half an onion
> 49baa1f adding ingredients and instructions
> ```
> 
> We want to continue to work on adapting the amount of cilantro:
> 
> ```
> $ git checkout a85267e 
> ```
>
> will show this message:
> 
> ```
> Note: checking out 'a85267e'.
> 
> You are in 'detached HEAD' state. You can look around, make experimental
> changes and commit them, and you can discard any commits you make in this
> state without impacting any branches by performing another checkout.
> 
> If you want to create a new branch to retain commits you create, you may
> do so (now or later) by using -b with the checkout command again. Example:
> 
>   git checkout -b <new-branch-name>
> 
> HEAD is now at a85267e maybe little bit less cilantro
> ```
>
> This means that the HEAD is not related to any branch
>
> * what is the output of `git branch`?
> * how does `git graph` look like?
>
> We see that the `HEAD` has moved to `a85267e`.
> Now we can do what Git suggests and create a branch from that state with:
>
> ```
> $ git checkout -b experiment_with_cilantro`
> ```
>
> * Inspect the result with `git branch` and `git graph`. What is the content of your current working directory?
>
> This is often used to create branches pointing to a commit in the past (e.g. if you want to inspect old versions of the project) and
> there is a shortcut for what we just did:
>
> ```
> $ git checkout -b <branchname> <hash>
> ```
>
> so we could have done `git checkout -b experiment_with_cilantro a85267e`
{: .challenge}


#### With `git reset`

*  `git reset --hard <hash>` can be used to make your current state identical with another branch/tag (represented by `<hash>`)  


> ## Exercise
>
> 1. Let's create few new commit on `experiment_with_cilantro` branch
> 2. Check how it goes with e.g. `git log` or `git graph`
> 3. Realize you do not want these commits and wish to go back to the original state of that branch. This can be done with
>
> ```
> $ git reset --hard a85267e 
> ```
>
> 4. What is now the output of `git graph` or `git log`?
{: .challenge}

* **`git reset` removes commits - be careful when using it on your work!**
* **As it rewrites history, it should not be used on commits that were pushed upstream.**
* `git reset --hard @{u}` is a command to make the local branch identical to upstream

---

### Clean history

After we have experimented, let us get our
repositories to a similar state.


```
$ git log --oneline

646c9e1 (HEAD -> master) Revert "testing a new idea"
e1d7745 testing a new idea
5861578 Merge branch 'less-salt'
0996fff Merge branch 'experiment'
372c868 add README.md file
721d9c6 reduce amount of salt
a85267e maybe little bit less cilantro
c79bfc1 let us try with some cilantro
901f422 enjoy your dish!
7adfe4b add half an onion
49baa1f adding ingredients and instructions

$ git reset --hard 901f422

HEAD is now at 901f422 we should not forget to enjoy

$ git log --oneline

901f422 (HEAD -> master) enjoy your dish!
7adfe4b add half an onion
49baa1f adding ingredients and instructions
```

---

### Undo uncommited changes  

* undo unstaged changes:

  - `git checkout -- <filename>`: **any local changes you made to that file/repository are gone**
  - `git checkout -b new-experiment && git add . && git commit -m "testing the new idea"`: commiting local changes to a new branch
  - `git stash save "testing a new idea"`: stash local changes

* undo staged but uncommited changes (you did `git add` but didn't do `git commit`):

  - `git reset HEAD` 


---

> ## Test your understanding
>
> 1. What happens if you accidentally remove a tracked file with `git rm`, is it gone forever?
> 2. Is it OK to modify commits that nobody has seen yet?
> 3. What situations would justify to modify the Git history and possibly remove commits?
> 4. What is the difference between these commands?
>    ```shell
>    $ git diff
>    $ git diff --staged  # or git diff --cached
>    $ git diff HEAD
>    $ git diff HEAD^
>    ```
>
> > ## Solution
> >
> > 1. It is not gone forever since `git rm` creates a new commit. You can simply revert it!
> > 2. If you haven't shared your commits with anyone it can be alright to modify them.
> > 3. If you have shared your commits with others (e.g. pushed them to GitHub), only extraordinary
> >    conditions would justify modifying history. For example to remove sensitive or secret information.
> > 4. The different commands show changes between different file states:
> >    ```shell
> >    $ git diff          # Show what has changed but hasn't been staged yet via git add.
> >    $ git diff --staged # Show what has been staged but not yet committed.
> >    $ git diff HEAD     # Show what has changed since the last commit.
> >    $ git diff HEAD^    # Show what has changed since the commit before the latest commit.
> >    ```
> {: .solution}
{: .challenge}

---

> ## Comment: Different meanings of "checkout"
>
> Depending on the context `git checkout` can do very different actions:
>
> 1) Switch to a branch:
>
> ```
> $ git checkout <branchname>
> ```
>
> 2) Bring the working tree to a specific state (commit), we will discuss that later:
>
> ```
> $ git checkout <hash>
> ```
>
> 3) Set a file/path to a specific state (**throws away all unstaged/uncommitted changes**):
>
> ```
> $ git checkout <path/file>
> ```
>
> This is unfortunate from the user's point of view but the way Git is implemented it makes sense.
> **Picture `git checkout` as an operation that brings the working tree to a specific state.**
> The state can be a commit or a branch (pointing to a commit).
>
> In latest Git this is much nicer:
> ```shell
> $ git switch <branchname>  # switch to a different branch
> $ git restore <path/file>  # discard changes in working directory
> ```
{: .callout}

---


## Recommendations, links, possible scenarios:

* when to use `git revert <hash>`:
	* if there is a commit in the project's history that (as you later decide) should not have been done and was a mistake
	* is the simplest to undo the last commit, reverting an older commit works the same way, but may cause conflicts (we will discuss that later)
	* it doesn't modify history, but it creates a new commit ('adds to history')

* when to use `git checkout`:
	* `git checkout` can be used in different contexts - see the comment above 
	* to get rid of all local changes in your working directory (be careful - those cannot be restored)
    * to bring your working tree to a specific state
    * it doesn't modify history 

* when to use `git reset --hard <hash>`:
	* you made commits (which you haven't shared with other people!) but later you decide that you don't want them
    * you want to make your branch identical with some other branch/tag
    * it moves HEAD to `<hash>` and resets your working directory to the state as in `<hash>` (so it looks like later commits were never made)
	* **it rewrites the history**


* Excellent guides:
	* [On undoing, fixing, or removing commits in git](http://sethrobertson.github.io/GitFixUm/fixup.html)
	* [How to undo all uncommitted changes](http://sethrobertson.github.io/GitFixUm/fixup.html#uncommitted_everything)

* [Play with undoing changes interactively](http://git-school.github.io/visualizing-git/)

