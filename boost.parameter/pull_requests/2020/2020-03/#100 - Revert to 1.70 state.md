# #100 Revert to 1.70 state [Merged]

> Username: Lastique  
> Created at: 2020-03-23 00:27:06 UTC  
> Updated at: 2020-03-23 15:28:02 UTC  
> Merged at: 2020-03-23 00:52:29 UTC  
> Closed at: 2020-03-23 00:52:29 UTC  
> Url: https://github.com/boostorg/parameter/pull/100  

This PR reverts Boost.Parameter to the state equivalent to `boost-1.70.0` tag plus the following changes re-applied from the develop branch:  
  
- Add basic CMake support. As part of this, I removed the dependency on Boost.MP11 as the 1.70 version did not use it.  
- Travis and AppVeyor CI configs restored to the latest state from develop.  
  
This should resolve issues https://github.com/boostorg/parameter/issues/91, https://github.com/boostorg/parameter/issues/95 and https://github.com/boostorg/parameter/issues/97.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-03-23 08:16:43 UTC  
> Url: https://github.com/boostorg/parameter/pull/100#issuecomment-602447826  

Thanks, Glen.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-03-23 08:36:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/100#issuecomment-602456040  

@glenfe It seems a number of Boost libraries were updated to use newer features of Boost.Parameter and are now failing. For example, Accumulators, Convert, Graph. Most notably, `boost/parameter/is_argument_pack.hpp` is now missing. I'll see if it can be restored without drastic changes to the rest of the code.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-03-23 14:55:05 UTC  
> Url: https://github.com/boostorg/parameter/pull/100#issuecomment-602650687  

@glenfe After looking more into this, it seems backporting some of the new features used in Boost libraries would require a significant effort. There are a few features, some of them are non-trivial and I don't have a good understanding of how Boost.Parameter internals work. The headers that are used in libraries and missing in 1.70 include: `is_argument_pack.hpp`, `nested_keyword.hpp`, `template_keyword.hpp`. There may be new features in the previously existing headers and also some of the new features could be used by users outside Boost. I'm also worried by the fact that I don't know how to test the changes I make.  
  
So, maybe reverting to 1.70 state was not such a good idea after all. :( I think we should revert this PR and go on with trying to fix it from that broken state. In particular, apply https://github.com/boostorg/parameter/pull/99.

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-03-23 15:05:00 UTC  
> Url: https://github.com/boostorg/parameter/pull/100#issuecomment-602656437  

@Lastique , OK. I'm going to (ultra vires) do the revert in an unorthodox fashion. Bear with me.

---

## Comment 5

> Username: glenfe  
> Created_at: 2020-03-23 15:09:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/100#issuecomment-602661618  

Done. We never attempted this rollback, and we've applied #99.

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-03-23 15:28:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/100#issuecomment-602674168  

Thanks again and sorry about the fuss.

---
