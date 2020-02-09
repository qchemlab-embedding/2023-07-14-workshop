---
layout: episode
title: Branching and merging. Part 1.
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

**Useful tip:** there is a shortcut for `git branch <name>` + `git checkout <name>`:

```shell
$ git checkout -b <name>   # create branch <name> and switch to it
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

## Before we continue with merging branches

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



