# #95 asciidoc customization [Open]

> Username: vinniefalco  
> Created at: 2024-09-13 18:03:38 UTC  
> Updated at: 2024-09-23 17:59:13 UTC  
> Url: https://github.com/boostorg/mp11/pull/95  

This adjusts the Jamfile to use the new boostlook templates for asciidoctor

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-09-13 18:12:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/95#pullrequestreview-2303770746  

📁 doc/boostdoc.css

```diff
   1 |+ /*! Asciidoctor default stylesheet | MIT License | https://asciidoctor.org */
```

> Username: pdimov  
> Created_at: 2024-09-13 18:12:47 UTC  
> Url: https://github.com/boostorg/mp11/pull/95#discussion_r1759284704  

Why does this file say "Asciidoctor default stylesheet | MIT license"? Is this even correct? Where is this file used?

> Username: vinniefalco  
> Created_at: 2024-09-13 18:36:02 UTC  
> Url: https://github.com/boostorg/mp11/pull/95#discussion_r1759312408  

Actually this file is not used, it was from earlier development before we moved things to the boostlook repo.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-09-16 22:54:27 UTC  
> Url: https://github.com/boostorg/mp11/pull/95#issuecomment-2354168749  

Do you like this new styling?

---

## Review 3 [Commented]

> Username: mborland  
> Created_at: 2024-09-23 16:48:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/95#pullrequestreview-2322826380  

📁 doc/Jamfile

```diff
   9 | 
  10 |- html mp11.html : mp11.adoc ;
  10 |+ path-constant boostlook : ../../../tools/boostlook ;
```

> Username: mborland  
> Created_at: 2024-09-23 16:48:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/95#discussion_r1771784331  

@grafikrobot Is this still correct with the modular build or should it be something like:  
  
```  
require-b2 5.0.1 ;  
import-search /boost/tools/boostlook ;  
```  
  
I'm not sure how the modular changes affected the doc builds.

> Username: grafikrobot  
> Created_at: 2024-09-23 17:59:13 UTC  
> Updated_at: 2024-09-23 18:00:31 UTC  
> Url: https://github.com/boostorg/mp11/pull/95#discussion_r1771868294  

@mborland yes. Except.. That boostlook doesn't have a b2 project to refer to. So it wouldn't work at this time. Which is why I created this PR https://github.com/boostorg/boostlook/pull/11


---
