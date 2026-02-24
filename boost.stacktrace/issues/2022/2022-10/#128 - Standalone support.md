# #128 - Standalone support? [Closed]

> Username: NAThompson  
> Created at: 2022-10-13 18:22:18 UTC  
> Updated at: 2024-09-12 07:53:52 UTC  
> Closed at: 2024-09-12 07:53:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/128  

How much effort would it be to support this library in standalone mode?  
  
From a cursory glance, it appears that `boost/config` is used only to detect existence of `#pragma once`, but that seems easy enough to remove.  
  
Essentially, we would like to use *just* this as a `git submodule`.

---

## Comment 1

> Username: ceztko  
> Created at: 2023-01-05 11:18:23 UTC  
> Updated at: 2023-01-05 11:20:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/128#issuecomment-1372089997  

Note to the author of `stacktrace`: pointing to other base library advancements that got standardized and offered a standalone version from the beginning, like [`date`](https://github.com/HowardHinnant/date) from Howard Hinnant or [`fmtlib`](https://github.com/fmtlib/fmt)  from Victor Zverovich shall not be an excuse for me or other people for not helping you in creating a standalone version of `stacktrace`, but quite frankly I want to say that having stuff standardized is one mean to reduce the dependency on big general purpose libraries such as boost, so I believe developing this with boost dependency from scratch was a conceptual mistake and it's just delaying the adoption of such helpful facility. I'm sure that many people coming here would love to use `stacktrace` today but will not because that would require importing boost for just a single feature.

---

## Comment 2

> Username: nigels-com  
> Created at: 2023-09-05 23:19:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/128#issuecomment-1707431437  

Coud well be that a majority here are already widely boost-entangled.  So it isn't high on our priority list, but by the sound of it, would not be a complicated patch.

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-09-12 07:52:52 UTC  
> Updated at: 2024-09-12 07:53:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/128#issuecomment-2345521654  

You could always try to make a standalone library by yourself and/or provide a patch here. I have no interest in such feature right now so I'm not planning to work on it.
