# #7 Replace 0 by nullptr [Merged]

> Username: MartinDelille  
> Created at: 2018-04-13 13:53:11 UTC  
> Updated at: 2019-06-07 11:59:38 UTC  
> Merged at: 2018-05-10 16:49:33 UTC  
> Closed at: 2018-05-10 16:49:33 UTC  
> Url: https://github.com/boostorg/foreach/pull/7  

Since I use [clazy](https://github.com/KDE/clazy) I have such warnings:  
  
```  
warning: zero as null pointer constant  
    foreach.hpp: 1100:77: note expanded from macro BOOST_FOREACH  
    foreach.hpp: 1014:9: note expanded from macro BOOST_FOREACH_CONTAIN  
    foreach.hpp: 942:13: note expanded from macro BOOST_FOREACH_SHOULD_COPY  
````  
  
This PR aims to fix this.

---

## Comment 1

> Username: MartinDelille  
> Created_at: 2018-04-16 11:57:21 UTC  
> Url: https://github.com/boostorg/foreach/pull/7#issuecomment-381574360  

Any idea why `nullptr` is not defined on linux?

---

## Comment 2

> Username: MartinDelille  
> Created_at: 2018-05-09 10:59:20 UTC  
> Url: https://github.com/boostorg/foreach/pull/7#issuecomment-387702412  

up

---

## Comment 3

> Username: ericniebler  
> Created_at: 2018-05-10 16:49:45 UTC  
> Url: https://github.com/boostorg/foreach/pull/7#issuecomment-388113313  

Thanks.

---

## Comment 4

> Username: MartinDelille  
> Created_at: 2019-01-18 10:58:33 UTC  
> Url: https://github.com/boostorg/foreach/pull/7#issuecomment-455508177  

@ericniebler I'd like to follow when this PR will be effective in the boost release version. Where could I track that?

---

## Comment 5

> Username: MartinDelille  
> Created_at: 2019-06-07 11:59:38 UTC  
> Url: https://github.com/boostorg/foreach/pull/7#issuecomment-499858020  

Any new about merging my contribution in the next release?  
  
The current boost version display annoying warnings.

---
