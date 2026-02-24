# #170 operators::adl is already base class of basic_type< T > [Closed]

> Username: bebuch  
> Created at: 2015-09-01 18:22:27 UTC  
> Updated at: 2015-09-02 16:59:08 UTC  
> Closed at: 2015-09-02 16:59:08 UTC  
> Url: https://github.com/boostorg/hana/pull/170  



---

## Comment 1

> Username: ldionne  
> Created_at: 2015-09-01 20:03:19 UTC  
> Updated_at: 2015-09-01 20:03:33 UTC  
> Url: https://github.com/boostorg/hana/pull/170#issuecomment-136844494  

This is going to fail on Clang. Agreed that I should try to understand what's going on instead of putting a band-aid by inheriting twice. Or at least add a comment.

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-09-01 20:33:12 UTC  
> Url: https://github.com/boostorg/hana/pull/170#issuecomment-136853226  

I admit being puzzled by the fact that this patch works. I could swear that I initially wrote it that way, saw it fail, and then cheaply worked around the failure by inheriting twice from `operators::adl`. Anyway, thanks; I will apply your commit locally to avoid a merge commit for such a small change.

---

## Comment 3

> Username: ldionne  
> Created_at: 2015-09-02 16:59:07 UTC  
> Url: https://github.com/boostorg/hana/pull/170#issuecomment-137170670  

Closing this, since 05a57e5 is already in develop.

---
