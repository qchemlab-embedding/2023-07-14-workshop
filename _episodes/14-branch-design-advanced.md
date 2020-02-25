---
layout: episode
title: "Git branches: advanced concepts"
teaching: 20
exercises: 0
questions:
  - What is the advantage of merging?
  - What is the advantage of rebasing?
  - Why is the Git log history important?
objectives:
  - Obtain a mental representation of the rebase model.
  - Learn how to name branches.
keypoints:
  - Rebasing creates nice linear history without merge commits, 
    but is associated with potential risks.
  - Having one main development branch which is documented, tested and has logical commit history is 
    useful to both developers and users.
---


## Git merge: recursive and fast-forward strategies


## Rebase vs. merge

To illustrate rebasing we consider the following situation - we wish to merge
`master` into `devel`:

![]({{ site.baseurl }}/img/branch-design/pre-rebase.svg)

Now you know how to do it:

```shell
$ git checkout devel
$ git merge master
```

This creates a merge commit:

![]({{ site.baseurl }}/img/branch-design/git-branch-08.svg)


But there is an alternative for integrating `master` commits into the `devel`
branch:

```shell
$ git checkout devel
$ git rebase master
```

![]({{ site.baseurl }}/img/branch-design/rebase.svg)

- `git rebase` replays the branch commits `b1` to `b3` on top of `master`.
- As if they were committed after `c5`.
- This **changes history** (notice that the commits `b1` to `b3` have been replaced by `b1*` to `b3*`).
- Discuss the advantages and disadvantages.

### Advantages and disadvantages

- `git rebase` makes "merges" producing a linear history.
- `git merge` resolves all conflicts in a single commit, with `git rebase` each commit may need
  conflict resolution.
- `git rebase` may invalidate tests.
- `git merge` preserves chronology of commits and creates explicit merge commits (unless fast-forward).
- `git rebase` can change chronology of commits.
- When working with others **do not rebase commits that other people depend on**
  (history has changed).
- Reference: "Treehouse of Horror V: Time and Punishment", The Simpsons (1994).

<br>
<br>
<img src="{{ site.baseurl }}/img/branch-design/simpsons.jpg" width="40%">

---


## Branch naming

- Name your local branches such that you will recognize them 3 months later.
- "test2", "foo", "debug", "mybranch" are not good branch names.
- Give descriptive names to remote branches.
- For feature branches we recommend to name them "author/topic" (example `joe/new-integrator`).
- Then everybody knows who is to be contacted about this branch (e.g. stale branches).
- Also you can easily find "your" branches:

```shell
$ git branch -r | grep joe
```

- Name bugfix branches after the issue/ticket (e.g. `issue-137`).
- For release branches we recommend e.g. `release/2.x` or `stable-2.x`.

- Organize branches according to features, not according to groups of people.
  - Good: branches `feature-a`, `feature-b`, `feature-c`.
  - Bad: branches `stockholm`, `san-francisco`, `helsinki`.
  - Reason: `stockholm`, `san-francisco`, and `helsinki` will either diverge
  (three main development lines) or somebody will spend a heroic effort to keep
  them synchronized.

---

## The main development line

- Always have only one main development line.
- Document where it is (there can be many forks, leave no doubt about where it is).
- Every commit on the main development line should build/compile.
- Reason: Sometimes you want to find a commit in the past that broke some functionality.
  - When using `git bisect` it is very helpful if all commits build/compile.
  - There is no reason to commit broken or unfinished code to the main development line: for this we have branches.

---

## Branch hygiene

Often we have this situation (newest commit is on top):

```shell
$ git log --oneline

6e129cf documentation for feature C
05344f6 small fix for feature C
bc11c47 save work on feature C
aa25177 feature B
6b58ba4 feature A
```

But we would prefer to have this history:

```shell
$ git log --oneline

81e100c feature C
aa25177 feature B
6b58ba4 feature A
```

- We can use `git reset --soft aa25177`.
- This means that we move our repository back to commit `aa25177`
  **but** we keep the working tree of the last committed state.
- The final state of the actual code is identical.
- Alternative to `git reset --soft` is an interactive rebase.
- We recommend to create commits on the main development line which are nice logical units.
- Commits should be pickable (not too large not too small for a `git cherry-pick`).
- Avoid ball-of-mud commits.

