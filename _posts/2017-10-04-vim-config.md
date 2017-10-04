---
layout: post
title: NOI Linux 下可用的 vimrc
date: 2017-10-04 22:13:00
categories: 杂
---

嘻嘻。

经过一点点琢磨，我写出了 NOI Linux 下可用的一个精简 vimrc ，支持这些功能：

1. 基本兼容 DevC++ 的 编译 `<C-F9>` 运行 `<C-F10>` 快捷键。
2. <del>可视模式下 `<C-/>` 给一段代码添加删除整齐的注释。</del> ( 懒得弄了 )

代码只有 20 行，应该可以背过：

```vim
set number
set cursorline
set cindent
set autoindent
set tabstop=4
set shiftwidth=4
set expandtab
set colorcolumn=80

function CompileProg()
    :make %:r
endfunction

function ExecuteProg()
    :silent !exec xterm -e "./%:r && echo ---- && echo Press any key && read"
    :redraw!
endfunction

nnoremap <C-F9> :call CompileProg()<CR>
nnoremap <C-F10> :call ExecuteProg()<CR>
```
