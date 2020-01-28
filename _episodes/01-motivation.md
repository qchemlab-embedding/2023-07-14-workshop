---
layout: episode
title: Motivation
teaching: 10
exercises: 0
questions:
  - Why version control?
  - Why Git?
objectives:
  - Make sure nobody leaves the workshop without starting to use some form of version control.
  - Discuss the reasons why we advocate distributed version control.
---

## The essence of version control

- System which **records snapshots** of a project
- Implements **branching**:
  - you can work on several feature branches and switch between them
  - different people can work on the same code/project without interfering
  - you can experiment with an idea and discard it if it turns out to be a bad idea
- Implements **merging**:
  - tool to merge development branches for you

---

## Without version control - a project can become a disaster:


```shell
>ls -l myproject/

myfile1.txt
myfile1_corrected.txt
myfile1.january.v1.txt
myfile1.january.v1.new.txt
myfile1.january.v1.new.corrected.txt
myfile1.january.v2.txt
myfile1.january.v2.save_chapter6.txt
...
```

### Roll-back functionality

- Mistakes happen - if you do not record snapshots, you cannot easily undo mistakes and go back to a working version.


### Branching

- Often you need to work on several issues in one project - without branching this can be messy and confusing.
- You can simulate branching by copying the entire directory to multiple places but also this will be messy and confusing.


### Collaboration

- *"I will just finish my work and then you can start with your changes."*.
- *"Can you please send me the latest version?"*.
- *"Where is the latest version?"*.
- *"Which version are you using?"*.
- *"Which version have the authors used in the paper I am trying to reproduce?"*.


### Reproducibility

- How do you indicate which version of your code you have used in your paper? How do you keep track of the analysis pipeline used in a project?
- When you find a bug in your code or a script, how do you know when precisely this bug was introduced? 
  (are published results affected? do you need to inform collaborators or users of your code or others using your analysis tools?).

---

> ## Why Git?
>
> We will use [Git](https://git-scm.com) to record snapshots of our work:
> - Easy to set up - use even by yourself with no server needed.
> - Very popular: chances are high you will need to contribute to somebody else's code which is tracked with Git.
> - Distributed: good backup, no single point of failure, you can track and clean-up changes offline, simplifies collaboration model for open-source projects.
> - Important platforms such as [GitHub](https://github.com), [GitLab](https://gitlab.com), and [Bitbucket](https://bitbucket.org)
>   build on top of Git.
> - Many platforms build on top of [GitHub](https://github.com).
> - Sharing software and data is getting popular and required in research context
>   and [GitHub](https://github.com) is a popular platform for sharing software.
> - However, *"Git is a four-handle, dual boiler espresso machine, not instant coffee."* [citation needed].
>   Git isn't the most user friendly and has its design quirks but deep design
>   is great and is definitely the most popular and what you are most likely to
>   need to know.
{: .discussion}

---

### Git vs Dropbox or Google Drive

- Document/code is in one place, no need to email snapshots.
- How can you use an old version? Possible to get old versions but in a much less useful way - snapshots of files, not directories.
- What if you want to work on multiple versions at the same time? Do you make a copy? How do you merge copies?
- What if you don't have internet?


