---
layout: post
title: "jekyll - vim"
description: ""
category: 
tags: [vim, jekyll]
---

***is there anything (that is useful, interesting, productive, etc) that is not
available in VIM/emacs?***

## [jekyll.vim](https://github.com/csexton/jekyll.vim/)

jekyll makes blogging extremely easy already, so to publish a new post, you
just need to :

1. type the following to generate the post file with rake:

        :!rake post title="my new post"

2. go locate the \_post folder and find the new post md file
3. open and edit the post
4. publish the post via git (have to make sure you are in the right dir)

        :!git add -A .
        :!git commit -m "a new post"
        :!git push origin master

## for lazy vimer
**is that all complex?**

for vim user, yes it is -- because it can be even simplified/automated.

after you install [jekyll.vim](https://github.com/csexton/jekyll.vim/) and
[fugitive](https://github.com/tpope/vim-fugitive) plugin, the above steps are
now:

1. you type :JekyllPost (or ",jp" if you are lazy and use key maps,as me) ,vim
   will ask you for a title. Just fill it (say, "my new post"). vim will compose
   a new post file for you in current buffer.
2. edit the post, just :w it when done
3. publish the post via fugitive (will intruduce its power in another post)

## even lazier..

**how about checking out texts from other text, and publish directly?**

naturely you have to copy the selected texts out of the file and paste them into
another buffer, edit , save, and publish it. so the key here is to mimic these
actions.

if you look at jekyll.vim code, all functions are defined and what you need is
just to "customize" the JekyllPost function and make it support *range*:


    function JekyllPostRange(title) range       <------
    "function JekyllPostRange(title)
      let published = g:jekyll_post_published
      let created = g:jekyll_post_created
      let tags = g:jekyll_prompt_tags
      let categories = g:jekyll_prompt_categories

      if created == "epoch"
        let created = localtime() 
      elseif created != ""
        let created = strftime(created)
      endif
      let title = a:title
      if title == ''
        let title = input("Post title: ")
      endif
      if tags != ""
        let tags = input("Post tags: ")
      endif
      if categories != ""
        let categories = input("Post categories: ")
      endif
      if title != ''
        let file_name = strftime("%Y-%m-%d-") . s:esctitle(title) . "." . g:jekyll_post_suffix
        echo "Making that post " . file_name
        exe a:firstline . "," . a:lastline . "yank"     <------

        exe "tabedit " . g:jekyll_path . "/_posts/" . file_name

        let template = ["---", "layout: post", "title: \"" . title . "\"", "published: " . published]
        if created != ""
          call add(template, "created:  "  . created)
        endif
        if tags != ""
          call add(template, "tags: [" . tags . "]")
        endif
        if categories != ""
          call add(template, "categories: [" . categories . "]")
        endif
        call extend(template,["---", ""])

        let err = append(0, template)

        normal! p       <------

      else
        call s:error("You must specify a title")
      endif
    endfunction
    "command! -nargs=? JekyllPost :call JekyllPost(<q-args>)   <------
    command! -range=% -nargs=* JekyllPostRange :<line1>,<line2>call JekyllPostRange(<q-args>)   <------


## test

now when I'm reading a text book and if you want to post the 3rd paragraph of
this book as a blog:
 
    Not to meander myself, at present, I will go back to my birth.

        *[mi'ændә]
        n. (河流的)蜿蜒, 漫步, 迂回旅行
        vi. 蜿蜒流动, 漫步
        vt. 使蜿蜒曲折

    I was born at Blunderstone, in Suffolk,  or 	我出生在萨福克的布兰德斯通，或
    'there by', as they say in Scotland.  I was a   者就像苏格兰人说的那样是“在那
    posthumous child. My father's eyes had closed   一边。”我是一个遗腹子。爸爸闭
    upon the light of this world six months, when   上眼六个月后我睁开了眼。就是现
    mine opened on it. There is something strange   在想到他竟从未见过我，我仍然觉
    to me, even now, in the reflection that he      得挺蹊跷的。而当回忆朦胧旧事时
    never saw me; and something stranger yet in     ，更令我觉得奇怪的是，他那块白
    the shadowy remembrance that I have of my       灰色的墓石竟是我儿时最初产生的
    first childish associations with his white      联想，每当我们的小客厅被火炉烧
    grave-stone in the churchyard, and of the       得暖烘烘，又被烛光照得亮堂堂时
    indefinable compassion I used to feel for it    ，我就对独自躺在黑夜里的父亲无
    lying out alone there in the dark night, when   限同情，想到他竟被我们关在门外
    our little parlour was warm and bright with     ，我简直觉得残忍不堪。
    fire and candle, and the doors of our house
    were--almost cruelly, it seemed to me
    sometimes--bolted and locked against it.

        *['pɒstjumәs] 	compassion
        a. 遗腹的, 死后的   n. 同情, 怜悯
        【医】 遗腹的 	reflection 反思

select the paragraph and type:

        vip:JekyllPostRange "david copperfield"

that's [it](http://pinggit.github.com/2013/02/17/david-copperfield/).

