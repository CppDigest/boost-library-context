# #58 Switch WINAPI calling convention macros to the replacements from Boost.WinAPI [Merged]

> Username: Lastique  
> Created at: 2018-03-11 17:38:43 UTC  
> Updated at: 2018-04-21 00:13:38 UTC  
> Merged at: 2018-03-12 11:35:48 UTC  
> Closed at: 2018-03-12 11:35:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/58  

WINAPI macro definition in Boost.WinAPI is deprecated as it may clash with the macro defined in Windows SDK.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-03-11 19:16:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/58#issuecomment-372141023  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/58?src=pr&el=h1) Report  
> Merging [#58](https://codecov.io/gh/boostorg/uuid/pull/58?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/421aa369ecdafcceb70960c578d539b32622d3e8?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/58/graphs/tree.svg?token=6falIr5npV&src=pr&height=150&width=650)](https://codecov.io/gh/boostorg/uuid/pull/58?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #58   +/-   ##  
========================================  
  Coverage    78.34%   78.34%             
========================================  
  Files           13       13             
  Lines          605      605             
  Branches       156      156             
========================================  
  Hits           474      474             
  Misses          17       17             
  Partials       114      114  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/58?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/58?src=pr&el=footer). Last update [421aa36...a75f5b0](https://codecov.io/gh/boostorg/uuid/pull/58?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Approved]

> Username: jeking3  
> Created_at: 2018-03-11 19:42:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/uuid/pull/58#pullrequestreview-102893490  

Thanks - does this need to get into master for 1.67.0?

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-03-11 19:50:18 UTC  
> Url: https://github.com/boostorg/uuid/pull/58#issuecomment-372143486  

On March 11, 2018 10:42:16 PM "James E. King, III"   
<notifications@github.com> wrote:  
  
> jeking3 approved this pull request.  
>  
> Thanks - does this need to get into master for 1.67.0?  
  
The updated version of Boost.WinAPI is only in develop. I'm not planning to   
merge it to master until after 1.67, unless I have to.

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-04-18 04:35:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/58#issuecomment-382257909  

This can be merged to master now.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-04-21 00:13:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/58#issuecomment-383252096  

Thanks for the reminder, I'll merge develop into master soon.

---
