---
layout: episode
title: Branching and merging
teaching: 10
exercises: 10
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

## Motivation for branches

In the previous section we tracked a guacamole recipe with Git.

Up until now our repository had only one branch with one commit coming
after the other:

![Linear]({{ site.baseurl }}/img/gitink/git-branch-1.svg "Linear git
repository"){:class="img-responsive"}

- Commits are depicted here as little boxes with abbreviated hashes.
- Here the branch `master` points to a commit.
- "HEAD" is the current position
- When we talk about branches, we often mean all parent commits, not only the commit pointed to.

### Now we want to do this:

<div style="border:2px solid #000000;">
  <img src="{{ site.baseurl }}/img/octopus.jpeg" width="60%">
  <br>
  Source: <a href="https://twitter.com/jay_gee/status/703360688618536960">https://twitter.com/jay_gee/status/703360688618536960</a>
</div>

Our work is often not linear. For instance when developing software:

- We typically need at least one version of the code to "work" (to compile, to give expected results, ...).
- At the same time we work on new features, often several features concurrently.
  Often they are unfinished.
- We need to be able to separate different lines of work really well.

The strength of version control is that it permits the researcher to **isolate
different tracks of work**, which can later be merged to create a composite
version that contains all changes:

![Git collaborative]({{ site.baseurl }}/img/gitink/git-collaborative.svg
"description"){:class="img-responsive"}

- We see branching points and merging points.
- Main line development is often called `master`.
- Other than this convention there is nothing special about `master`, it is just a branch.
- Commits form a directed acyclic graph (we have left out the arrows to avoid confusion about the time arrow).

A group of commits that create a single narrative are called a **branch**.
There are different branching strategies, but it is useful to think that a branch
tells the story of a feature, e.g. "fast sequence extraction" or "Python interface" or "fixing bug in
matrix inversion algorithm".

---

> ## A useful alias
>
> We will now define an *alias* in Git, to be able to nicely visualize branch
> structure in the terminal without having to remember a long Git command
>
> ```shell
> $ git config --global alias.graph "log --all --graph --decorate --oneline"
> ```
{: .callout}

Let us inspect the project history using the `git graph` alias:

```shell
$ git graph

* 901f422 (HEAD -> master) enjoy your dish!
* 7adfe4b add half an onion
* 49baa1f adding ingredients and instructions
```

- We have three commits and only one development line (branch) and this branch is called `master`.
- Commits are states characterized by a 40-character hash (checksum).
- `git graph` print abbreviations of these checksums.
- Branches are pointers that point to a commit.
- Branch `master` points to commit `901f4226d0700e89aaf45265fd6dac9368dad974`.
- `HEAD` is another pointer, it points to where we are right now (currently `master`)

### On which branch are we?

To see where we are (where HEAD points to) use `git branch`:

```shell
$ git branch

* master
```

- This command shows where we are, it does not create a branch.
- There is only `master` and we are on `master` (star represents the `HEAD`).

In the following we will learn how to create branches,
how to switch between them, how to merge branches,
and how to remove them afterwards.

---

## Creating and working with branches

Let's create a branch called `experiment` where we add cilantro to `ingredients.txt`.

```shell
$ git branch experiment    # create branch called "experiment" pointing to the present commit
$ git checkout experiment  # switch to branch "experiment"
$ git branch               # list all local branches and show on which branch we are
```

- Verify that you are on the `experiment` branch (note that `git graph` also
  makes it clear what branch you are on: `HEAD -> branchname`):

```shell
$ git branch

* experiment
  master
```

- Then add 2 tbsp cilantro **on top** of the `ingredients.txt`:

```shell
* 2 tbsp cilantro
* 2 avocados
* 1 lime
* 2 tsp salt
* 1/2 onion
```

- Stage this and commit it with the message "let us try with some cilantro".
- Then reduce the amount of cilantro to 1 tbsp, stage and commit again with "maybe little bit less cilantro".

We have created **two new commits**:

```shell
$ git graph

* 6feb49d (HEAD -> experiment) maybe little bit less cilantro
* 7cf6d8c let us try with some cilantro
* dd4472c (master) we should not forget to enjoy
* 2bb9bb4 add half an onion
* 2d79e7e adding ingredients and instructions
```

- The branch `experiment` is two commits ahead of `master`.
- We commit our changes to this branch.

---

> ## Exercise: branches
>
> - Change to the branch `master`.
> - Create another branch called `less-salt`
>   where you reduce the amount of salt.
> - Commit your changes to the `less-salt` branch.
> - Go back to `experiment` branch
>
> Use the same commands as we used above.
>
> We now have three branches (in this case `HEAD` points to `experiment`):
>
> ```shell
> $ git branch
>
> * experiment
>   less-salt
>   master
>
> $ git graph
>
> * 721d9c6 (less-salt) reduce amount of salt
> | * a85267e (HEAD -> experiment) maybe little bit less cilantro
> | * c79bfc1 let us try with some cilantro
> |/  
> * 901f422 (master) enjoy your dish!
> * 7adfe4b add half an onion
> * 49baa1f adding ingredients and instructions
> ```
>
> Here is a graphical representation of what we have created:
>
> ![]({{ site.baseurl }}/img/gitink/git-branch-2.svg)
>
> - Now switch to `master`.
> - Add and commit the following `README.md` to `master`:
>
> ```markdown
> # Guacamole recipe
>
> Used in teaching Git.
> ```
>
> Now you should have this situation:
>
> ```shell
> $ git graph
>
> * 372c868 (HEAD -> master) draft a README.md file
> | * 721d9c6 (less-salt) reduce amount of salt
> |/  
> | * a85267e (experiment) maybe little bit less cilantro
> | * c79bfc1 let us try with some cilantro
> |/  
> * 901f422 enjoy your dish!
> * 7adfe4b add half an onion
> * 49baa1f adding ingredients and instructions
> ```
>
> ![]({{ site.baseurl }}/img/gitink/git-branch-3.svg)
>
> And for comparison this is how it looks [on GitHub](https://github.com/coderefinery/recipe/network).
{: .challenge}

---

## Merging branches

**If you got stuck in the above exercises**:

- **Skip this unless you got stuck**.
- Step out of the current directory: `$ cd ..`
- Then:
  ```
  $ git clone https://github.com/coderefinery/recipe.git recipe-branching
  $ cd recipe-branching
  $ git checkout experiment
  $ git checkout less-salt
  $ git checkout master
  $ git graph
  ```
- Or call a helper to un-stuck it for you.


It turned out that our experiment with cilantro was a good idea.
Our goal now is to merge `experiment` into `master`.

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

Deleted branch experiment (was 6feb49d).
Deleted branch less-salt (was bf59be6).
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

Git will not let you delete a branch which has not been reintegrated unless you
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

Since the following command combo is so frequent:

```shell
$ git branch <name>        # create branch <name>
$ git checkout <name>      # switch to branch <name>
```

There is a shortcut for it:

```shell
$ git checkout -b <name>   # create branch <name> and switch to it
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


