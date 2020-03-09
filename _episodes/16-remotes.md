---
layout: episode
title: Working with remotes. Distributed version control.
teaching: 15
exercises: 0
questions:
  - How can we share repositories with others?
  - How can we keep repositories in sync?
objectives:
  - Understand different workflows within the distributed version control.
keypoints:
  - "`git clone` copies everything and sets some pointers to remember where the clone came from."
  - "`origin` is just an alias."
  - We can add and remove remotes.
  - We can have multiple remotes.
  - We can call these aliases as we like.
  - We synchronize remotes via the local clone.
  - "To see all remotes use `git remote -v`."
---


# Motivation

- Someone has given you access to a repository online and you want to contribute to it.
- You would like others to contribute to your online repository.


# Basics

We already discussed some basics of working with remotes in [this lesson](../08-remotes).

## Cloning repositories

The `git clone` command makes a copy of a repository.  For example,

```shell
$ git clone https://host.com/user/project.git project
```

- Contributing to a repository often starts by cloning the entire repository.
- By cloning we clone all commits, all branches and tags, **entire history**.
- **A clone is a full-fledged repository.**

---

## Synchronizing changes between repositories

- We need a mechanism to communicate changes between the repositories.
- We will **pull** or **fetch** updates **from** remote repositories (we will discuss the difference between pull and fetch).
- We will **push** updates **to** remote repositories.

---

# Distributed version control

Git implements a **distributed** version control.

This means that any type of repository links that you can think of can be
implemented - not just "everything connects to one central server".

So every developer can do both:
* contribute code to other repositories
* maintain a public repository on which others can base their work and which they can contribute to. 

Two topologies are very frequent: centralized and forking layout.


### Centralized layout

![]({{ site.baseurl }}/img/git-collaborative/forking/centralized.svg)

In Git, **all repositories are equivalent** but in the typical **centralized** style, we consider one repository
as the main development line and this is marked as "central".
The "central" is a role, not a technical difference.

Features:

- Typically all developers have both read and write permissions (double-headed arrows).
- Suited for cases where all developers are in the same group or organization etc.
- Code review workflow is possible.
- Code review can be coupled with with automated testing.

Advantages:

- More familiar for Subversion or CVS users.
- Easier: for each clone there is only one remote.

Disadvantages:

- Everybody who wants to contribute needs write access.
- Maintainer needs to trust the developers to not break things (but you can protect branches).

Real life examples:

- [https://github.com/ropensci/plotly](https://github.com/ropensci/plotly)


### Forking layout

![]({{ site.baseurl }}/img/git-collaborative/forking/forking-overview.svg)

In the **forking layout**, again we call one repository the "central"
repository but people push to **forks** (their own copies of the
repository on Github).

Features:

- Most developers have only read access to the main project.
- For a public repository everybody has read access.
- Only very few people (the maintainers) have write access.
- Typically nobody pushes directly to the central repo.
- Code review can be coupled with with automated testing.
- Central repo and the forks typically reside in the "cloud".
- Naturally grows out the centralized model once more people begin
  contributing.

Advantages:

- Code is integrated via code review (during pull/merge request).
- Maintainer has full control over what goes in.
- Allows contributions from people you don't know yet (in practice not possible in centralized layout).
- Structurally helps to implement peer review in coding (code review).

Disadvantages:

- Learning curve: we need to deal at least with two remotes (fork and central repo).
- Introduces additional steps (to e.g. update the fork).

Real life examples:

- NumPy: [https://numpy.org/devdocs/dev/index.html](https://numpy.org/devdocs/dev/index.html)
- [https://github.com/jupyterlab/jupyterlab](https://github.com/jupyterlab/jupyterlab)

---


## More on remotes:

- In [this lesson](../08-remotes) we learnt how to set the remote's url and how to change it:

```shell
$ git remote add origin <git-repo-url>
$ git remote set-url origin <new-git-url>
```

- There is nothing special about the name `origin`. The `origin` is just an alias.
- We can call these aliases as we like.
- We can have multiple remotes.
- We can add and remove remotes:

```shell
$ git remote add upstream https://github.com/project/project.git
$ git remote rm upstream
$ git remote add group-repo https://example.com/exciting-project.git
$ git remote rm group-repo
```

We synchronize remotes via the local clone.

To see all remotes:

```shell
$ git remote --verbose
```

* Technically, a **remote** repository is only used as a collaboration point, it’s just the Git data.
* Git can use different protocols to transfer data: Local, HTTP, SSH and Git (more on that [here](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)).


<br>
<br>
<br>
![]({{ site.baseurl }}/img/git-collaborative/forking/remote.jpg)
<br>

**Luke Skywalker:** *You know, I did feel something. I could almost see the remote.*

**Ben Kenobi:** *That's good. You've taken your first step into a larger world.*

(from Star Wars Episode IV - A New Hope)

<br>
<br>
<br>

---


