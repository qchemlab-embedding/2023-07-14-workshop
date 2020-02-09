---
layout: default
permalink: /helper/git-settings
---

# Set up Git from the command line:

* detailed instructions are on [CodeRefinery](https://coderefinery.github.io/installation/git/)

* basic setup (if you copy-paste the commands below, omit the '$' symbol):

```shell
$ git config --global user.name "Your Name"
$ git config --global user.email yourname@example.com
$ git config --global core.editor your-editor
```

your-editor: vim, emacs, nano, any other of your choice (if you work on Windows, please check [these instructions](https://coderefinery.github.io/installation/git/))


* to verify your setup from the command line run `git config VARIABLE`; for instance to check what is the current `user.name` used by Git run:

```shell
$ git config user.name
```

* all current Git settings can be listed with:

```shell
$ git config --list
```

* to see where this Git setup is stored:

```shell
$ git config --list --show-origin
```


* if you want to set up Git locally (for a particular repository), omit the `--global` argument in `git config` commands above


* useful Git aliases, which we use in Git workshops:

```shell
$ git config --global alias.graph "log --all --graph --decorate --oneline
```

# Create GitHub account

* create an account on [GitHub](https://github.com/)
* to connect to Github without the need to type your login and password each time, set up your SSH keys by following the steps described in [this tutorial](https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

