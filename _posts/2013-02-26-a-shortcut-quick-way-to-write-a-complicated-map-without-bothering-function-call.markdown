---
layout: post
title: "a shortcut/quick way to write a complicated map without bothering function call!"
published: true
tags: vim
---


"(2013-02-26) 
"a shortcut/quick way to do a complicated map but bypassing function calls

    map ,ct :
        \if g:colors_name == 'default' <Bar>
            \colorscheme koehler <Bar>
        \else <Bar>
            \colorscheme default <Bar>
        \endif
        \<CR>
