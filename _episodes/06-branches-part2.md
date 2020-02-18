---
layout: episode
title: Branching and merging. Part 2.
teaching: 20
exercises: 15
questions:
  - How can I or my team work on multiple features in parallel?
  - How to combine the changes of parallel tracks of work?
  - How can I permanently reference a point in history, like a software
    version?
objectives:
  - Be able to create and merge branches.
  - Know the difference between a branch and a tag.
keypoints:
  - A branch is a division unit of work, to be merged with other units of work.
---

## Git branches - quick recap:

* create a branch: `git branch <name-of-a-branch>`
* check out a branch: `git checkout <name-of-a-branch>`
* create and check out a branch in one step: `git checkout -b <name-of-a-branch>` 
* list all branches: `git branch`
* alias useful to work with branches: `git graph`, it was created with `git config --global alias.graph "log --all --graph --decorate --oneline"`


---

## Examine differences between branches

We continue working in **recipe** directory:

```shell
$ git branch

  experiment
  less-salt
* master
```

Let's examine what is different on the experiment branch with respect to the (current) master branch.


> ## Exercise:
>
> Experiment with these commands, while you are on a `master` branch:
>
> ```shell
> $ git diff experiment
> $ git diff --name-status experiment
> $ git diff --name-status experiment..master
> $ git diff --name-status master..experiment
> $ git diff --stat experiment
> $ git diff experiment..master -- ingredients.txt
> $ git log experiment..master
> $ git log --oneline --graph --decorate --abbrev-commit experiment..master
> ```
{: .challenge}


**Tip:** also try visual diff tools (run `git difftool --tool-help` to see all predefined options)

---

## Merging branches

It turned out that our experiment with cilantro was a good idea.
Our goal now is to **merge `experiment` into `master`**.

First we make sure we are on the branch we wish to merge **into**:

```shell
$ git branch

  experiment
  less-salt
* master
```

Then we merge `experiment` into `master`:

```shell
$ git merge experiment
```

![]({{ site.baseurl }}/img/gitink/git-merge-1.svg)

We can verify the result in the terminal:

```shell
$ git graph

*   0996fff (HEAD -> master) Merge branch 'experiment'
|\  
| * a85267e (experiment) maybe little bit less cilantro
| * c79bfc1 let us try with some cilantro
* | 372c868 add README.md file
|/  
| * 721d9c6 (less-salt) reduce amount of salt
|/  
* 901f422 enjoy your dish!
* 7adfe4b add half an onion
* 49baa1f adding ingredients and instructions
```

What happens internally when you merge two branches is that Git creates a new
commit, attempts to incorporate changes from both branches and records the
state of all files in the new commit. While a regular commit has one parent, a
merge commit has two (or more) parents.

To view the branches that are merged into the current branch we can use the command:

```shell
$ git branch --merged

  experiment
* master
```

We are also happy with the work on the `less-salt` branch. Let us merge that
one, too, into `master`:

```shell
$ git branch  # make sure you are on master
$ git merge less-salt
```

![]({{ site.baseurl }}/img/gitink/git-merge-2.svg)

We can verify the result in the terminal:

```shell
$ git graph

*   5861578 (HEAD -> master) Merge branch 'less-salt'
|\  
| * 721d9c6 (less-salt) reduce amount of salt
* |   0996fff Merge branch 'experiment'
|\ \  
| * | a85267e (experiment) maybe little bit less cilantro
| * | c79bfc1 let us try with some cilantro
| |/  
* | 372c868 add README.md file
|/  
* 901f422 enjoy your dish!
* 7adfe4b add half an onion
* 49baa1f adding ingredients and instructions
```

Observe how Git nicely merged the changed amount of salt and the new ingredient **in the same file
without us merging it manually**:

```shell
$ cat ingredients.txt

* 1 tbsp cilantro
* 2 avocados
* 1 lime
* 1 tsp salt
* 1/2 onion
```

If the same file is changed in both branches, Git attempts to incorporate both
changes into the merged file. If the changes overlap then the user has to
manually *settle merge conflicts* (we will do that later).

---

## Deleting branches safely

Both feature branches are merged:

```shell
$ git branch --merged

  experiment
  less-salt
* master
```

This means we can delete the branches:

```shell
$ git branch -d experiment less-salt

Deleted branch experiment (was a85267e).
Deleted branch less-salt (was 721d9c6).
```

This is the result:

![]({{ site.baseurl }}/img/gitink/git-deleted-branches.svg)

Compare in the terminal:

```shell
$ git graph

*   5861578 (HEAD -> master) Merge branch 'less-salt'
|\  
| * 721d9c6 reduce amount of salt
* |   0996fff Merge branch 'experiment'
|\ \  
| * | a85267e maybe little bit less cilantro
| * | c79bfc1 let us try with some cilantro
| |/  
* | 372c868 add README.md file
|/  
* 901f422 enjoy your dish!
* 7adfe4b add half an onion
* 49baa1f adding ingredients and instructions
```

As you see only the pointers disappeared, not the commits.

Git **will not let you delete a branch which has not been reintegrated** unless you
insist using `git branch -D`. Even then your commits will not be lost but you
may have a hard time finding them as there is no branch pointing to them.

---

## Summary

Let us pause for a moment and recapitulate what we have just learned:

```shell
$ git branch               # see where we are
$ git branch <name>        # create branch <name>
$ git checkout <name>      # switch to branch <name>
$ git merge <name>         # merge branch <name> (to current branch)
$ git branch -d <name>     # delete branch <name>
$ git branch -D <name>     # delete unmerged branch
```


### Typical workflows

With this there are two typical workflows:

```shell
$ git checkout -b new-feature  # create branch, switch to it
$ git commit                   # work, work, work, ...
                               # test
                               # feature is ready
$ git checkout master          # switch to master
$ git merge new-feature        # merge work to master
$ git branch -d new-feature    # remove branch
```

Sometimes you have a wild idea which does not work.
Or you want some throw-away branch for debugging:

```shell
$ git checkout -b wild-idea
                               # work, work, work, ...
                               # realize it was a bad idea
$ git checkout master
$ git branch -D wild-idea      # it is gone, off to a new idea
                               # -D because we never merged back
```

No problem: we worked on a branch, branch is deleted, `master` is clean.

---

> ## Test your understanding
>
> 1. Which of the following combos (one or more) creates a new branch and makes a commit to it?
>
>    1.
>    ```shell
>    $ git branch new-branch
>    $ git add file.txt
>    $ git commit
>    ```
>    2.
>    ```shell
>    $ git add file.txt
>    $ git branch new-branch
>    $ git checkout new-branch
>    $ git commit
>    ```
>    3.
>    ```shell
>    $ git checkout -b new-branch
>    $ git add file.txt
>    $ git commit
>    ```
>    4.
>    ```shell
>    $ git checkout new-branch
>    $ git add file.txt
>    $ git commit
>    ```
>
> > ## Solutions
> >
> > 1. Both 2 and 3 would do the job. Note that in 2 we first stage the file, and then create the
> >    branch and commit to it. In 1 we create the branch but do not switch to it, while in 4 we
> >    don't give the `-b` flag to `git checkout` to create the new branch.
> {: .solution}
{: .challenge}

---

> ## Tags
>
> - A tag is a pointer to a commit but in contrast to a branch it does not move.
> - We use tags to record particular states or milestones of a project at a given
>   point in time, like for instance versions (have a look at [semantic versioning](http://semver.org),
>   v1.0.3 is easier to understand and remember than 64441c1934def7d91ff0b66af0795749d5f1954a).
> - There are two basic types of tags: annotated and lightweight.
> - **Use annotated tags** since they contain the author and can be cryptographically signed using
>   GPG, timestamped, and a message attached.
>
> Let's add an annotated tag to our current state of the guacamole recipe:
>
> ```shell
> $ git tag -a nobel-2017 -m "recipe I made for the 2017 Nobel banquet"
> ```
>
> As you may have found out already, `git show` is a very versatile command. Try this:
>
> ```shell
> $ git show nobel-2017
> ```
>
> For more information about tags see for example
> [the Pro Git book](https://git-scm.com/book/en/v2/Git-Basics-Tagging) chapter on the
> subject.
{: .challenge}


