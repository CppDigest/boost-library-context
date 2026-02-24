# #1 Deal with .git* files in inspect, and remove some redundant checks. [Closed]

> Username: danieljames  
> Created at: 2013-12-15 18:13:12 UTC  
> Updated at: 2013-12-20 15:58:27 UTC  
> Closed at: 2013-12-20 15:58:10 UTC  
> Url: https://github.com/boostorg/inspect/pull/1  

Since visit_predicate is only used for directories, and in a modular check out `.git` can be a file, inspect is complaining that `.git` starts with an unacceptable character. It's also complaining about `.gitattributes`, so I made it allow anything that start with `.git`.  
  
Also remove some checks that don't do anything, so they probably could give the misleading idea that `.htaccess` and `.DS_Store` files are ignored.

---

## Comment 1

> Username: danieljames  
> Created_at: 2013-12-20 15:58:10 UTC  
> Url: https://github.com/boostorg/inspect/pull/1#issuecomment-31019369  

Made redundant by a442ae0017caa2d25fc792a7769d2d9523d5a7a0

---
