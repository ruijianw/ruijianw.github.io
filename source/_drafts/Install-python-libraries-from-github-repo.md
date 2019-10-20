---
title: Install python libraries from github repo
tags:
---

Apart from `pip install` and `conda install`, you can also directly install the python libraries from github(without explicitly `git clone`)

# Movtivation

If you are using a open source library, and you realize there is small bug. Certainly you can direct modify the file under your `python3.7/site-packages/[some library]`. If you want to use it in a docker container, it would be more convenient to install from the github repo.

# How
If you want to install it via command line, just do:
`pip install git+git://git.example.com/MyProject.git@master#egg=MyProject`

Or you can keep the string after `git+` in the `requirements.txt` file, pip can also pick it up

You may also want to avoid tha cache by adding `--no-cache-dir` in pip command.

The example above we use `@master` to represent master branch, in addition to the branch name, we can also use `commit hash`, `tag`, `refs`(e.g. `@refs/pull/123/head`)



---

# Referenece:

1. https://pip.pypa.io/en/stable/reference/pip_install/#git
2. 