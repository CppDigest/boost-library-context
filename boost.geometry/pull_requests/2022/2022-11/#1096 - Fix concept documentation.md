# #1096 Fix concept documentation [Merged]

> Username: vissarion  
> Created at: 2022-11-17 15:03:53 UTC  
> Updated at: 2022-12-01 09:06:37 UTC  
> Merged at: 2022-12-01 09:06:31 UTC  
> Closed at: 2022-12-01 09:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1096  

A fix for https://github.com/boostorg/geometry/issues/954

---

## Comment 1

> Username: vissarion  
> Created_at: 2022-11-29 13:19:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1096#issuecomment-1330643592  

@barendgehrels @awulkiew could you please give a quick feedback here?

---

## Review 2 [Changes requested]

> Username: barendgehrels  
> Created_at: 2022-11-30 11:47:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1096#pullrequestreview-1191642256  

Status set to request changes (=answers :-) )

📁 include/boost/geometry/geometries/concepts/point_concept.hpp

```diff
  38 |- /*!
  39 |- \brief Point concept.
  40 |- \ingroup concepts
```

> Username: barendgehrels  
> Created_at: 2022-11-23 13:23:07 UTC  
> Updated_at: 2022-11-30 11:47:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1096#discussion_r1030438343  

But where does all this go now?

> Username: barendgehrels  
> Created_at: 2022-11-30 11:46:46 UTC  
> Updated_at: 2022-11-30 11:47:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1096#discussion_r1035870262  

@vissarion could you answer this? I'm a bit puzzled that all this is removed, and I don't see a replacement anywhere...

> Username: vissarion  
> Created_at: 2022-11-30 12:53:16 UTC  
> Updated_at: 2022-11-30 12:53:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1096#discussion_r1035932364  

It seems to me a duplicate of https://github.com/boostorg/geometry/blob/develop/doc/concept/point.qbk   
The same holds for the concepts of other geometries, the doc system uses the information in https://github.com/boostorg/geometry/blob/develop/doc/concept and it seems that the inline doxygen comments in `include/boost/geometry/geometries/concepts/*_concept.hpp` files are not used. Am I missing something?

> Username: barendgehrels  
> Created_at: 2022-11-30 17:08:40 UTC  
> Updated_at: 2022-11-30 17:08:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1096#discussion_r1036235354  

Perfect, thank you! I grepped for some of the terms, but they are ticked in `.qbk` so I didn't find them.  
  
OK then of course, thank you!


---
