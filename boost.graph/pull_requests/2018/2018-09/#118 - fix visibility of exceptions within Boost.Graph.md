# #118 fix visibility of exceptions within Boost.Graph [Merged]

> Username: apolukhin  
> Created at: 2018-09-25 08:09:48 UTC  
> Updated at: 2018-10-04 20:53:55 UTC  
> Merged at: 2018-09-29 14:14:44 UTC  
> Closed at: 2018-09-29 14:14:44 UTC  
> Url: https://github.com/boostorg/graph/pull/118  

This is required to avoid issues with `-fvisibility=hidden` builds (those are now default in Boost develop branch).  
  
More info on the macro available [here](https://www.boost.org/doc/libs/1_68_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_for_libraries_with_separate_source_code.macros_controlling_shared_library_symbol_visibility)  
  
Related issue: https://github.com/boostorg/graph_parallel/pull/16

---

## Comment 1

> Username: anadon  
> Created_at: 2018-09-25 14:35:20 UTC  
> Url: https://github.com/boostorg/graph/pull/118#issuecomment-424368682  

The relaxed heap failure is currently addressed in PR #114.  It might actually be related to why #114 is failing Mac builds, and if so I'll look into merging this in.  Thanks for helping out!

---

## Comment 2

> Username: anadon  
> Created_at: 2018-09-25 14:50:10 UTC  
> Url: https://github.com/boostorg/graph/pull/118#issuecomment-424375318  

It looks like this kind of change doesn't change code functionality, only linking.  It also appears that this kind of change could be applied more broadly.  Are there other things which should be guarded with `BOOST_SYMBOL_VISIBLE`?

---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-09-26 07:24:20 UTC  
> Url: https://github.com/boostorg/graph/pull/118#issuecomment-424612972  

> Are there other things which should be guarded with `BOOST_SYMBOL_VISIBLE`?  
  
No. Only new exception types. Other things should be OK.

---
