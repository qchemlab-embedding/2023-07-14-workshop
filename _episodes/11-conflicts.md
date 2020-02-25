---
layout: episode
title: Conflict resolution
teaching: 15
exercises: 15
questions:
  - How can we resolve conflicts?
  - How can we avoid conflicts?
objectives:
  - Understand merge conflicts sufficiently well to be able to fix them.
keypoints:
  - Conflicts often appear because of not enough communication or not optimal branching strategy.
---

## Conflict resolution

<img src="{{ site.baseurl }}/img/conflict-resolution/conflict.png" width="60%">

In most cases a `git merge` runs smooth and automatic.
Then a merge commit appears (unless fast-forward: we will come back to that) without you even noticing.

Git is very good at resolving modifications when merging branches.

But sometimes the same line or portion of the code/text is modified on two branches and Git issues a **conflict**.
Then you need to tell Git which version to keep (**resolve** it).

There are several ways to do that as we will see.

Please remember:

- Conflicts look scary, but are not that bad after a little bit of practice. Also they are luckily rare.
- Don't be afraid of Git because of conflicts. You may not meet some conflicts using other systems because you simply can't do the kinds of things you do in Git.
- You can take human measures to reduce them.

---

## Type-along: create a conflict

We go back to our **recipe** example. 
If you don't have it, please do:

```shell
$ git clone git@github.com:comp-sci-tools/recipe_merged.git fresh_recipe
$ cd fresh_recipe
```

Now we will make two branches and make two conflicting changes (both increase
and decrease the amount of cilantro), and then try to merge them
together.  Git won't decide which to take for you, so will present it
to you for deciding.  We do that and commit again to resolve the
conflict.

- Create two branches from `master`: one called `like-cilantro`, one called `dislike-cilantro`:

```shell
$ git graph

*   f0272c2 (HEAD -> master, origin/master, origin/HEAD, like-cilantro, dislike-cilantro) Merge branch 'less-salt'
|\  
| * 89d5730 (origin/less-salt) reduce amount of salt
* |   0e6c5b1 Merge branch 'experiment'
|\ \  
| * | d4d360e (origin/experiment) maybe little bit less cilantro
| * | b859fe2 let us try with some cilantro
| |/  
* | b100795 draft a README.md file
|/  
* 78d6661 enjoy your dish!
* 8094aee add half an onion
* 4c02218 adding ingredients and instructions
```

- On the two branches make **different modifications** to the amount of the **same ingredient**:

```shell
$ git graph

* b542667 (dislike-cilantro) reduce the amount of cilantro
| * 5a35ac6 (like-cilantro) we try with more cilantro
|/  
*   f0272c2 (HEAD -> master, origin/master, origin/HEAD) Merge branch 'less-salt'
|\  
| * 89d5730 (origin/less-salt) reduce amount of salt
* |   0e6c5b1 Merge branch 'experiment'
|\ \  
| * | d4d360e (origin/experiment) maybe little bit less cilantro
| * | b859fe2 let us try with some cilantro
| |/  
* | b100795 draft a README.md file
|/  
* 78d6661 enjoy your dish!
* 8094aee add half an onion
* 4c02218 adding ingredients and instructions
```

On the branch `like-cilantro` we have the following change:

```
$ git diff master like-cilantro
```

```diff
diff --git a/ingredients.txt b/ingredients.txt
index b9eaea2..2c7c2fd 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -1,4 +1,4 @@
-* 1 tbsp cilantro
+* 2 tbsp cilantro
 * 2 avocados
 * 1 lime
 * 1 tsp salt
```

And on the branch `dislike-cilantro` we have the following change:

```
$ git diff master dislike-cilantro
```

```diff
diff --git a/ingredients.txt b/ingredients.txt
index b9eaea2..51010d8 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -1,4 +1,4 @@
-* 1 tbsp cilantro
+* 0.5 tbsp cilantro
 * 2 avocados
 * 1 lime
 * 1 tsp salt
```

### What do you expect will happen when we try to merge these two branches into master?

The first merge will work:

```shell
$ git checkout master
$ git status
$ git merge like-cilantro

Updating f0272c2..5a35ac6
Fast-forward
 ingredients.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

But the second will fail:


```shell
$ git merge dislike-cilantro

Auto-merging ingredients.txt
CONFLICT (content): Merge conflict in ingredients.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Without conflict Git would have automatically created a merge commit,
but since there is a conflict, Git did not commit:

```shell
$ git status

On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   ingredients.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Observe how Git gives us clear instructions on how to move forward.

Let us inspect the conflicting file:

```
$ cat ingredients.txt

<<<<<<< HEAD
* 2 tbsp cilantro
=======
* 0.5 tbsp cilantro
>>>>>>> dislike-cilantro
* 2 avocados
* 1 lime
* 1 tsp salt
* 1/2 salt
```

Git inserted resolution markers (the `<<<<<<<`, `>>>>>>>`, and `=======`).

Try also `git diff`:

```
$ git diff
```

```diff
diff --cc ingredients.txt
index 2c7c2fd,51010d8..0000000
--- a/ingredients.txt
+++ b/ingredients.txt
@@@ -1,4 -1,4 +1,8 @@@
++<<<<<<< HEAD
 +* 2 tbsp cilantro
++=======
+ * 0.5 tbsp cilantro
++>>>>>>> dislike-cilantro
  * 2 avocados
  * 1 lime
  * 1 tsp salt
```

`git diff` now only shows the conflicting part, nothing else.

We have to resolve the conflict.
We will discuss 3 different ways to do this.

---

## Manual resolution

```
<<<<<<< HEAD
* 2 tbsp cilantro
=======
* 0.5 tbsp cilantro
>>>>>>> dislike-cilantro
```

We have to edit the code/text between the resolution markers.  You
only have to care about what git shows you: Git stages all files
without conflicts and leaves the files with conflicts unstaged.

Simple steps:

- Check status with `git status` and `git diff`.
- Decide what you keep (the one, the other, or both or something
  else).  Edit the file to do this.
  - Remove the resolution markers, if not already done.
  - The file(s) should now look exactly how you want them.
- Check status with `git status` and `git diff`.
- Tell Git that you have resolved the conflict with `git add ingredients.txt`
  (if you use the Emacs editor with a certain plugin the editor may stage the
  change for you after you have removed the conflict markers).
- Verify the result with `git status`.
- Finally commit the merge with just `git commit` - everything is pre-filled.

---

> ## Exercise: Create another conflict and resolve
>
> 1. After you have merged `like-cilantro` and `dislike-cilantro` create again two branches.
> 2. Again modify some ingredient on both branches.
> 3. Merge one, merge the other and observe a conflict, resolve the conflict and commit the merge.
> 4. What happens if you apply the same modification on both branches?
{: .challenge}

---

## Using "ours" or "theirs" strategy

- Sometimes you know that you want to keep "ours" version (version on this branch)
  or "theirs" (version on the merged branch).
- Then you do not have to resolve conflicts manually.
- See [merge strategies](https://git-scm.com/docs/merge-strategies).

Example:

```shell
$ git merge -s recursive -Xours less-avocados  # merge and in doubt take the changes from current branch
```

Or:

```shell
$ git merge -s recursive -Xtheirs less-avocados  # merge and in doubt take the changes from less-avocados branch
```

---

## Aborting a conflicting merge

- Imagine it is Friday evening, you try to merge but have conflicts all over the place.
- You do not feel like resolving it now and want to undo the half-finished merge.
- Or it is a conflict that you cannot resolve and only your colleague knows which version is the one to keep.

What to do?

- There is no reason to delete the whole repository.
- You can undo the broken merge by resetting the repository to `HEAD` (last committed state).

```shell
$ git merge --abort
```

The repository looks then exactly as it was before the merge.

---

> ## (Optional) Resolution using mergetool
>
> - Again create a conflict (for instance disagree on the number of avocados).
> - Stop at this stage:
>
> ```
> Auto-merging ingredients.txt
> CONFLICT (content): Merge conflict in ingredients.txt
> Automatic merge failed; fix conflicts and then commit the result.
> ```
>
> - Instead of resolving the conflict manually, use a visual tool
>   (requires installing one of the [visual diff tools](https://coderefinery.github.io/installation/difftools/)):
>
> ```shell
> $ git mergetool
> ```
>
> <img src="{{ site.baseurl }}/img/conflict-resolution/mergetool.png" width="100%">
>
> - Your current branch is left, the branch you merge is right, result is in the middle.
> - After you are done, close and commit, `git add` is not needed when using `git mergetool`.
>
> If you have not instructed Git to avoid creating backups when using mergetool, then to be on
> the safe side there will be additional temporary files created. To remove those  you can do
> a git clean after the merging.
>
> To view what will be removed:
>
> ```
> $ git clean -n
> ```
>
> To remove:
>
> ```
> $ git clean -f
> ```
>
> To configure Git to avoid creating backups at all:
>
> ```
> $ git config --global mergetool.keepBackup false
> ```
{: .challenge}

---

