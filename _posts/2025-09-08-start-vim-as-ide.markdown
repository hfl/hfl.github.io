---
layout: post
title: 启用 VIM 做为 IDE 
categories: vim
date: 2025-09-08 07:30:34 +0800
---

[Debian](https://debian.org) 升级到了 13（trixie），一切看起来很好——除了 [Apache 2](https://httpd.apache.org) 不能启动和 [gEdit](https://gedit-text-editor.org) 丢失了Snippets。

Apache 2 不能启动就不启动了，反正启动的就是我的本机的博客软件 [Textpattern](https://textpattern.com)，平时
也不怎么用，所以不着急，慢慢调整吧。

gEdit 是我平时编写软件的 IDE，虽然功能简单，好在我的要求也不高——只需要能够提供括号和
引号的自动完成简单的代码提示即可，而 gEdit 本身提供的插件恰好提供这些功能，另外，
gEdit 还提供一个 Git 的代码提示功能，而这些就是我对编程软件的全部要求。所以，gEdit 一
直是我推崇的编程软件。但是，Debian 升级到 13，gEdit 莫名其妙就没有了代码提示功能，经
查询，发现 gEdit 新版本不提供代码提示功能了——为了精简软件代码！看来是时候离开 gEdit 了。

于是开始重启 VIM 之旅，重新使用永远的 VIM！我的 VIM 使用 Plug 管理插件，`.vimrc` 如下：

~~~
set number
call plug#begin('~/.vim/plugged')
Plug 'tpope/vim-sensible'
Plug 'jiangmiao/auto-pairs'
Plug 'preservim/nerdtree'
call plug#end
~~~

`vimrc` 文件我会实时更新，对所使用的每个插件会做解释。

本期我们要解释

 `set number`：为编辑界面设置行数显示。

[Plug](https://github.com/junegunn/vim-plug)：一款极简的 Vim 插件管理器。编写方法如上面的代码。命令有下面几个：

* `:PlugInstall`：用于安装插件
* `:PlugUpdate`：用于安装或者更新插件
* `:PlugDiff`：预览插件最近更新的修改状况
* `:PlugClean`：移除列表中不再使用的插件

该软件使用简单，速度极快。
