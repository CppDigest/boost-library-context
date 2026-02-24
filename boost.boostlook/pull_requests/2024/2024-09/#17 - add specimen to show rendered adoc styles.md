# #17 add specimen to show rendered adoc styles [Closed]

> Username: mborland  
> Created at: 2024-09-23 20:55:01 UTC  
> Updated at: 2024-09-24 16:01:55 UTC  
> Closed at: 2024-09-24 15:59:23 UTC  
> Url: https://github.com/boostorg/boostlook/pull/17  

This converts all of the charconv docs into one giant page and adds the normal docfooter and Jamfile so that rendering can be added to the PRs.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2024-09-23 21:06:11 UTC  
> Url: https://github.com/boostorg/boostlook/pull/17#issuecomment-2369386753  

"specimen"

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-09-23 21:11:20 UTC  
> Updated_at: 2024-09-23 21:11:34 UTC  
> Url: https://github.com/boostorg/boostlook/pull/17#issuecomment-2369396838  

Can we please use the jam module as shown here:  
https://github.com/boostorg/boostlook/pull/11/commits/9b55bf4d2ca06466d345afa061854cab848f480c  
  
It would simplify it to:  
```  
html specimen.html : specimen.adoc  
    :   <use>/boost/boostlook//boostlook  
        <dependency>specimen-docinfo-footer.html  
        ;  
```

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2024-09-23 21:24:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/17#pullrequestreview-2323347016  

📁 doc/Jamfile

```diff
   7 | 
   8 | path-constant boostlook : ../../../tools/boostlook ;
```

> Username: vinniefalco  
> Created_at: 2024-09-23 21:24:13 UTC  
> Updated_at: 2024-09-23 21:24:14 UTC  
> Url: https://github.com/boostorg/boostlook/pull/17#discussion_r1772099791  

This is probably no longer needed as it is not used


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2024-09-23 21:38:19 UTC  
> Url: https://github.com/boostorg/boostlook/pull/17#issuecomment-2369487811  

before merge can we please rename "speciman" to "specimen" ?

---
