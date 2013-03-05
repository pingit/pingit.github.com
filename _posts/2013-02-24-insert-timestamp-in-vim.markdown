---
layout: post
title: "insert timestamp in vim"
published: true
tags: vim
---


    "insert a timestamp in curr cursor place(and return to normal)
    "(02/24/2013) change format to def jekyll(01/15/2001 to 2001-01-15)
    nn ,di i(<esc>"=strftime("%Y-%m-%d")<CR>pa)<space><esc>
    nn ,da a(<esc>"=strftime("%Y-%m-%d")<CR>pa)<space><esc>
    nn ,Di i(<esc>"=strftime('%c')<CR>pa)<space><esc>
    nn ,Da a(<esc>"=strftime('%c')<CR>pa)<space><esc>
    ino <F5> (<C-R>=strftime("%Y-%m-%d")<CR>)<space>
    "this doesn't work in tmux
    ino <C-F5> (<C-R>=strftime('%c')<CR>)<space>
