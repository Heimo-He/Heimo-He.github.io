---
title: MacOS command 
categories: macos
tags:
 - macos
---

- 关闭/开启rootless(System Integrity Protection),进入恢复模式(重启，长按command+R)

`csrutil disablecsrutil enable`

- 在Finder标题栏显示完整路径

`defaults write com. apple. finder. FXShowPosixPathInTitle -bool true`

<!-- more -->

- 显示/隐藏Finder隐藏文件，快捷键⇧+⌘+.

`defaults write com.apple.finder AppleShowAllFiles -bool true ; killall Finder`
`defaults write com. apple. finder AppleShowAllFiles -bool false ; killall Finder`

- 禁用/启用dashboard:

`defaults write com. apple. dashboard mcx-disabled -bool true && killall Dock`
`defaults write com. apple. dashboard mcx-disabled -bool false && killall Dock`

- 禁止/恢复生成.DS_Store:

`defaults write com. apple.desktopservices DSDontWriteNetworkStores true`
`defaults delete com. apple. desktopservices DSDontWriteNetworkStores`

- 删除.DS_Store

`sudo find / -name " .DS_ Store" -depth -exec rm {} \;`

- 允许从任何来源安装应用

`sudo spctl --master-disable`

- 查看CPU信息:

`sysctl -n machdep.cpu.brand_ string`



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/09/11/macos-base-command/](https://heimo-he.github.io/php/2018/09/11/macos-base-command/)***
