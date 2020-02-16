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

We start with checking where we are (`git status`, `git log`, `git branch`, `git diff`, `git graph`):

![]({{ site.baseurl }}/img/gitink/git-deleted-branches.svg)

  - what do we want to undo?
  - review different states of a file in Git (untracked, modified, staged, commited)
  - review the meaning of index and SHA



> #### Clear your workspace
>
> - If you have unstaged changes from earlier sections, remove them with `git checkout <filename>`.
> - We will see in more detail below how `git checkout` works.
>
{: .callout}



### Undo uncommited changes  

* undo unstaged changes:

  - `git checkout -- <filename>`: **any local changes you made to that file/repository are gone**
  - `git checkout -b new-experiment && git add . && git commit -m "testing the new idea"`:
  - `git stash save "testing a new idea"`

* undo staged but uncommited changes (you did `git add` but didn't do `git commit`):

  - `git reset HEAD <filename>` 

* excellent guide: [link](http://sethrobertson.github.io/GitFixUm/fixup.html#uncommitted_everything)

---

### Reverting commits

- Let's make a new commit on a `master` branch (e.g. modify a README.md file)
- We realize that the latest commit `e1d7745` was a mistake and we wish to undo it:

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

### Clean history

After we have experimented with reverts and amending, let us get our
repositories to a similar state.

At the same time we will learn how to remove commits (use this command with caution in your work).

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
> Picture `git checkout` as an operation that brings the working tree to a specific state.
> The state can be a commit or a branch (pointing to a commit).
>
> In latest Git this is much nicer:
> ```shell
> $ git switch <branchname>  # switch to a different branch
> $ git restore <path/file>  # discard changes in working directory
> ```
{: .callout}


