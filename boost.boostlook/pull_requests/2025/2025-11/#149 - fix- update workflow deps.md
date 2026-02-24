# #149 fix: update workflow deps [Closed]

> Username: julioest  
> Created at: 2025-11-20 19:48:01 UTC  
> Updated at: 2025-11-20 23:33:26 UTC  
> Closed at: 2025-11-20 23:33:25 UTC  
> Url: https://github.com/boostorg/boostlook/pull/149  

Fixes gh CLI authentication error when triggering website-v2-docs workflows.  
  
Added `gh auth login --with-token` before workflow trigger commands to   
authenticate the GitHub CLI with the PAT token.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-11-20 19:55:33 UTC  
> Updated_at: 2025-11-20 23:30:32 UTC  
> Url: https://github.com/boostorg/boostlook/pull/149#issuecomment-3559794935  

An automated preview of the documentation is available at [https://149.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://149.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-11-20 23:30:31 UTC

---
