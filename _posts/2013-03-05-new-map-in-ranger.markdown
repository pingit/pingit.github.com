---
layout: post
title: "new map in ranger"
published: true
tags: [ranger]
categories: [tech]
---

put following in rc.conf:

    #ping: per gmail thread ranger: how-tos or cheat-sheet
    map ,r chain shell vim ~/.config/ranger/rc.conf; source ~/.config/ranger/rc.conf
    map ,a shell vim ~/.config/ranger/rifle.conf; source ~/.config/ranger/rifle.conf
    map ,c shell vim ~/.config/ranger/commands.py;
    map ,s shell vim ~/.config/ranger/scope.sh;

whenever you need to edit ranger config file, just type ",r", ranger will open
rc.conf file for you. good thing is , right after you finish the config, ranger
will re-source the config file so the changes you just made will be in effect
"in the fly".
