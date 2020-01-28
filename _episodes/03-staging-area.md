---
layout: episode
title: Using the Git staging area
teaching: 10
exercises: 0
questions:
  - Why do we recommend to first add, then commit a change?
  - What should be included in a single commit?
objectives:
  - Demystify the Git staging area.
  - Learn how to tell a story with your commit history.
keypoints:
  - The staging area helps us to create well-defined commits.
---


## Commit history is telling a story

Commit history is telling a story about your project:

- Projects/code are rarely self-documenting.
- We only see what is now, we don't know how things came to this stage.
- It would be great if all changes were documented, but that is too much to ask.
- What is a good compromise?

Git forces you to create version history and commit messages,
and if these are clear then you are a long way to **organized project**.


> ## Discussion
>
> To motivate why we have always first staged with `git add` and then committed with `git commit`
> let us discuss three examples:
>
> Example 1 (newest commit is on top):
>
> ```shell
> b135ec8 now feature A should work
> 72d78e7 feature A did not work and started work on feature B
> bf39f9d more work on feature B
> 49dc419 wip
> 45831a5 removing debug prints for feature A and add new file
> bddb280 more work on feature B and make feature A compile again
> 72e0211 another fix to make it compile
> 61dd3a3 forgot file and bugfix
> ```
>
> Example 2 (newest commit is on top):
>
> ```shell
> 6f0d49f implement feature C
> fee1807 implement feature B
> 6fe2f23 implement feature A
> ```
>
> Example 3:
>
> ```shell
> ab990f4 saving three months of miscellaneous uncommitted work
> ```
>
> Let's discuss these examples. Can you anticipate problems?
{: .challenge}

We want to have nice commits.  But we also want to "save often"
(checkpointing) - how can we have both?

- We will now learn to create nice commits using the *staging area*.
- Staging addresses the issue of having unrelated changes in the same
commit or having one logical change spread over several commits.
- The staging area isn't the only way to organize your history nicely, some alternatives are discussed at the end of the lesson.

---

> ## Analogies to the staging area: moving boxes
>
>
> ### [Analogy using moving boxes](https://dev.to/sublimegeek/git-staging-area-explained-like-im-five-1anh)
>
> <img src="{{ site.baseurl }}/img/ikea_box.jpg" width="30%">
>
> - You're moving and you have a box to pack your things in.
> - You can put stuff into the box, but you can also take stuff out of the box.
> - You wouldn't want to mix items from the bathroom, kitchen and living room into the same box.
> - The box corresponds to the staging area of Git, where you can craft your commits.
> - Committing is like sealing the box and sticking a label on it.
> - You wouldn't want to label your box with "stuff", but rather give a more descriptive label.
>
> In order to keep organized, you have to use multiple locations to
> stage things in sequence.
{: .discussion}

---

## States of a file

![]({{ site.baseurl }}/img/file_states.png)

*Note: the "staging area" has also alternatively been referred to as
the index and the cache*.

Files can be untracked, modified, staged, or committed, and
we have a variety of commands to go between states:

```shell
$ git add <path>       # stages all changes in file
$ git add -p <path>    # stages while letting you choose which lines to take
$ git commit           # commits the staged change
$ git diff             # see **unstaged** changes
$ git diff --staged    # see **staged** changes
$ git rm               # removes a file
$ git reset            # unstages staged changes
                       # in latest Git: git restore --staged <path>
$ git checkout <path>  # check out the latest staged version ( or committed
                       # version if file has not been staged )
                       # in latest Git: git restore <path>
```

**Recommendation:**
- `git add` every change that improves the code.
- `git checkout` every change that made things worse.
- `git commit` as soon as you have created a nice self-contained unit (not too large, not too small).
- Discuss/think about what is too large or too small.


### Example workflow

```shell
$ git add file.py                 # checkpoint 1
$ git add file.py                 # checkpoint 2
$ git add another_file.py         # checkpoint 3
$ git add another_file.py         # checkpoint 4
# ... further work on another_file.py ...
$ git diff another_file.py        # diff w.r.t. checkpoint 4
$ git checkout another_file.py    # oops go back to checkpoint 4
$ git commit                      # commit everything that is staged
```

> ## Exercise: Using the staging area
>
> 1. In your recipe example, make two different changes to
>   `ingredients.txt` and `instructions.txt` which do not go together.
> 2. Use `git add` to stage one of the changes.
> 3. Use `git status` to see what's going on, and use `git diff` and `git diff --staged` to see the changes.
> 4. Feel some regret and unstage the staged change.
{: .challenge}

---

> ## Test your understanding
>
> - When is it better to "save" a change as commit, when is it better to "save" it with `git add`?
> - Is it a problem to commit many small changes?
> - What types of problems can occur in other version control systems without a staging area?
{: .challenge}
