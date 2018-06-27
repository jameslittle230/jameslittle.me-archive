---
title:  "The Most Efficient Github SSH Key Generation Process"
date:   2018-03-20
summary: "I generate SSH keys a lot. Here's how to make them (and add them to your Github account) as fast as humanly possible."
---

I generate SSH keys a lot. Here's how to make them (and add them to your Github account) as fast as humanly possible.

1. <https://github.com/settings/ssh/new>
2. `ssh-keygen -t rsa -b 4096 -C "littleguy23"` 
3. `eval "$(ssh-agent -s)"; ssh-add ~/.ssh/id_rsa; cat ~/.ssh/id_rsa.pub`
4. Copy and paste the terminal output into the Github page

Title courtesy of [Hammacher Schlemmer](https://www.hammacher.com/product/most-efficient-fireplace-grate).