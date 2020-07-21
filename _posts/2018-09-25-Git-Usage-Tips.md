---
title: "Git Usage Tips"
date: 2018-09-25
tag: Git
excerpt_separator: <!--more-->
---

对于git的配置是从哪里来的呢，我一直很疑惑，所以看到一个git config --list --show-origin非常有用，因此记录一下
<!--more-->

## Git usage tips

Recently I maybe will use git a lot, for now i only know several simple command, so i want to write the frequently used command down for later usage:

today i find a command very useful to find out all the configuration's origin, aka, where do the config value come from

### 1. show config origin, helps me a lot

```text
git config --list --show-origin

file:"C:\\ProgramData/Git/config"       core.symlinks=false
file:"C:\\ProgramData/Git/config"       core.autocrlf=false
file:"C:\\ProgramData/Git/config"       core.fscache=true
file:"C:\\ProgramData/Git/config"       color.diff=auto
file:"C:\\ProgramData/Git/config"       color.status=auto
file:"C:\\ProgramData/Git/config"       color.branch=auto
file:"C:\\ProgramData/Git/config"       color.interactive=true
file:"C:\\ProgramData/Git/config"       help.format=html
file:"C:\\ProgramData/Git/config"       rebase.autosquash=true
file:C:/MySDK/Git/mingw64/etc/gitconfig http.sslcainfo=C:/MySDK/Git/mingw64/ssl/certs/ca-bundle.crt
file:C:/MySDK/Git/mingw64/etc/gitconfig http.sslbackend=openssl
file:C:/MySDK/Git/mingw64/etc/gitconfig diff.astextplain.textconv=astextplain
file:C:/MySDK/Git/mingw64/etc/gitconfig filter.lfs.clean=git-lfs clean -- %f
file:C:/MySDK/Git/mingw64/etc/gitconfig filter.lfs.smudge=git-lfs smudge -- %f
file:C:/MySDK/Git/mingw64/etc/gitconfig filter.lfs.process=git-lfs filter-process
file:C:/MySDK/Git/mingw64/etc/gitconfig filter.lfs.required=true
file:C:/MySDK/Git/mingw64/etc/gitconfig credential.helper=manager
file:C:/MySDK/Git/mingw64/etc/gitconfig core.editor='C:\Program Files (x86)\Notepad++\notepad++.exe' -multiInst -notabbar -nosession -noPlugin
file:C:/Users/Administrator/.gitconfig  gui.recentrepo=C:/Users/Administrator/front-end-dev-book
file:C:/Users/Administrator/.gitconfig  user.email=
file:C:/Users/Administrator/.gitconfig  user.name=
```
