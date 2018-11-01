---
title: 解决GitHub提交历史头像不显示问题
categories: git
tags:
 - git
---


- 在该项目下执行`git config user.email`，所显示的邮箱肯定不是github邮箱

- 执行`git config user.email "github邮箱"`，修改的是该项目的user email

- 如果想修改所有的项目，可直接修改全局配置`git config --global user.email "github邮箱"`



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/macos/2018/11/01/github-commit-avatar/](https://heimo-he.github.io/macos/2018/11/01/github-commit-avatar/)***