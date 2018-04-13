---
title: brew报错
categories: macos
tags: 
 - brew
 - macos
---

## 报错如下

```bash
➜  jekyll-jacman git:(master) brew upgrade ruby
Updating Homebrew...
==> Homebrew has enabled anonymous aggregate user behaviour analytics.
Read the analytics documentation (and how to opt-out) here:
  https://docs.brew.sh/Analytics

xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
Error: Failure while executing: git config --local --replace-all homebrew.analyticsmessage true
==> Upgrading 1 outdated package, with result:
ruby 2.5.1
==> Upgrading ruby
==> Installing dependencies for ruby: openssl
==> Installing ruby dependency: openssl
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
Error: Failure while executing: git config --local --replace-all homebrew.private true
```

<!-- more -->

## 解决办法

```bash
xcode-select --install
```



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/2018/03/30/brew-error/](https://heimo-he.github.io/2018/03/30/brew-error/)***