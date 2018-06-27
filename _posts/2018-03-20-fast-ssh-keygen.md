---
title:  "The most efficient Github SSH key generation process"
date:   2018-03-20
---

1. <https://github.com/settings/ssh/new>
2. `ssh-keygen -t rsa -b 4096 -C "littleguy23"` 
3. `eval "$(ssh-agent -s)"; ssh-add ~/.ssh/id_rsa; cat ~/.ssh/id_rsa.pub`
4. Copy and paste the terminal output into the Github page
