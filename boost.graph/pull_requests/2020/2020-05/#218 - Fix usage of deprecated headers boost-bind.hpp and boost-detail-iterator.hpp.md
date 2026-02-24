# #218 Fix usage of deprecated headers boost/bind.hpp and boost/detail/iterator.hpp [Merged]

> Username: Lastique  
> Created at: 2020-05-11 17:15:55 UTC  
> Updated at: 2021-05-07 20:26:54 UTC  
> Merged at: 2020-11-02 15:39:06 UTC  
> Closed at: 2020-11-02 15:39:06 UTC  
> Url: https://github.com/boostorg/graph/pull/218  

These headers emit deprecation warnings and are subject to future removal.  
  
Also, added a few missing includes.

---

## Comment 1

> Username: sdebionne  
> Created_at: 2020-09-16 07:42:33 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-693234168  

@Lastique Thanks for fixing these annoying warnings, I see that the same modifications in property_map were merged OK. Looking at the failing CI jobs, it looks like it is related to XCode, a typo in filesystem (`st_mtim` instead of `st_mtime`). That might have been fixed, any chance that you could trigger the CI again?

---

## Comment 2

> Username: sdebionne  
> Created_at: 2020-09-16 15:27:58 UTC  
> Updated_at: 2020-09-25 12:40:26 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-693482356  

@jzmaddock Can this please be merged ?

---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-11-02 13:09:11 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-720461897  

@jzmaddock @glenfe Could this be merged for 1.75, please?

---

## Comment 4

> Username: fkonvick  
> Created_at: 2021-04-13 12:01:58 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-818680358  

@jzmaddock  This still does not seem to be in 1.76 - as of rc1 boost/graph/adjacency_iterator.hpp still #includes boost/detail/iterator.hpp

---

## Comment 5

> Username: hebaishi  
> Created_at: 2021-04-17 01:37:59 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-821745590  

Just checked the boost 1.76.0 release and this isn't in there. 😢

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-04-17 10:54:52 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-821804918  

Sorry guys, looks like I dropped the ball on that.  Let me see what state CI is in, and then try and do a merge to master...

---

## Comment 7

> Username: fkonvick  
> Created_at: 2021-04-17 14:48:00 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-821834206  

No problem, better safe than sorry.  I'm fine waiting for the next release :)  Thanks.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-04-20 17:45:40 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-823476718  

Now merged to master.

---

## Comment 9

> Username: jwakely  
> Created_at: 2021-05-07 20:26:54 UTC  
> Url: https://github.com/boostorg/graph/pull/218#issuecomment-834752803  

Why does Boost still use such an error-prone branch workflow that requires manually merging individual fixes? :cry:

---
