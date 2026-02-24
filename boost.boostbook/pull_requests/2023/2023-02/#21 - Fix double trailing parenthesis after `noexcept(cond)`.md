# #21 Fix double trailing parenthesis after `noexcept(cond)` [Merged]

> Username: Lastique  
> Created at: 2023-02-26 01:50:36 UTC  
> Updated at: 2023-02-26 14:26:40 UTC  
> Merged at: 2023-02-26 13:06:32 UTC  
> Closed at: 2023-02-26 13:06:32 UTC  
> Url: https://github.com/boostorg/boostbook/pull/21  

This removes the redundant trailing parenthesis after `noexcept(cond)` function qualifiers.  
  
Fixes https://github.com/boostorg/boostbook/issues/20.  
  
Also clears executable permission from an XSLT file.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-02-26 12:18:00 UTC  
> Updated_at: 2023-02-26 12:27:01 UTC  
> Url: https://github.com/boostorg/boostbook/pull/21#issuecomment-1445347011  

@glenfe I'm not sure who is maintaining BoostBook these days, but since you merged PRs here, could you take a look, please? It would be nice to have this fixed in the next release.

---

## Comment 2

> Username: glenfe  
> Created_at: 2023-02-26 13:07:26 UTC  
> Url: https://github.com/boostorg/boostbook/pull/21#issuecomment-1445357684  

@Lastique Thanks for fixing issues like this. After the develop doc build completes, is the a library's docs we can compare between develop and master to see the effect of the noexcept change?

---

## Comment 3

> Username: Lastique  
> Created_at: 2023-02-26 14:25:07 UTC  
> Url: https://github.com/boostorg/boostbook/pull/21#issuecomment-1445374578  

I referenced [Boost.Intrusive](https://www.boost.org/doc/libs/1_81_0/doc/html/boost/intrusive/hashdata_internal.html#idm32877-bb) docs as an example in #20.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-02-26 14:26:39 UTC  
> Url: https://github.com/boostorg/boostbook/pull/21#issuecomment-1445374908  

And thanks for merging, BTW. Please, remember to merge to master as well.

---
