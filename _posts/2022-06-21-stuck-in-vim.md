---
layout: post
title: Help! I'm stuck in vim!
date: 2022-06-21
categories: vim
slug: stuck-in-vim
---
Do not fear.

`ESC` `:q!` `ENTER`

## Explanation

`ESC` will get you out of any mode you may have activated and bring you back to *normal* mode, where you can enter commands.

`:q!` is the command for 'quit without saving'.

## For future reference

If you ever have to use vim again then remember this - if you press `i` while in *normal* mode, you'll enter *insert* mode, which behaves pretty much like a normal text editor. Once you're done, press `ESC` to return to *normal* mode, then enter the command `:wq` to 'write and quit'.