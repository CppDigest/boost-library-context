# #70 Introduce top-level gil.hpp header [Merged]

> Username: stefanseefeld  
> Created at: 2018-04-03 17:20:12 UTC  
> Updated at: 2020-03-10 20:32:08 UTC  
> Merged at: 2018-04-03 18:13:57 UTC  
> Closed at: 2018-04-03 18:13:57 UTC  
> Url: https://github.com/boostorg/gil/pull/70  

This introduces a new top-level header to be in alignment with Boost standard practice. All tests that used to include `boost/gil/gil_all.hpp` have been changed accordingly. The original header hasn't been changed yet. We should consider deprecating it, and remove it in a future release.

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2018-04-03 17:47:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/70#pullrequestreview-109049924  

📁 include/boost/gil.hpp

```diff
   9 |+ #define boost_gil_hpp_
  10 |+ 
  11 |+ #include <boost/gil/gil_config.hpp>
```

> Username: mloskot  
> Created_at: 2018-04-03 17:45:25 UTC  
> Updated_at: 2018-04-03 17:52:54 UTC  
> Url: https://github.com/boostorg/gil/pull/70#discussion_r178907118  

`#include <boost/gil/version.hpp>` is missing

> Username: stefanseefeld  
> Created_at: 2018-04-03 17:54:35 UTC  
> Url: https://github.com/boostorg/gil/pull/70#discussion_r178909816  

Added, thanks.


📁 test/bug.cpp~

```diff
   1 |+ /*
```

> Username: mloskot  
> Created_at: 2018-04-03 17:47:18 UTC  
> Updated_at: 2018-04-03 17:52:54 UTC  
> Url: https://github.com/boostorg/gil/pull/70#discussion_r178907640  

This backup `bug.cpp~` file should not be included.  
  
We may be missing `*~` or similar from `.gitignore`?

> Username: stefanseefeld  
> Created_at: 2018-04-03 17:51:11 UTC  
> Updated_at: 2018-04-03 17:52:54 UTC  
> Url: https://github.com/boostorg/gil/pull/70#discussion_r178908802  

True, we should add `*~` to `.gitignore`. Meanwhile, I have removed `test/bug.cpp` and `test/bug.cpp~` as unintended. Thanks for the quick review, and spotting those !


---

## Comment 2

> Username: mloskot  
> Created_at: 2018-04-03 18:00:55 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378341684  

Thanks for working on this

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-04-03 20:14:21 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378381483  

BTW, are we sure this is going to be released in Boost 1.68?  
I wonder if we should have `Boost.GIL X.Y` milestones too.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-04-03 20:17:31 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378382698  

Boost 1.68 is due to be released in ~6 months. I sure hope we'll have managed to stabilize and merge the `develop` branch long before that ! But sure, feel free to create other milestones beyond `1.68` if you want to plan longer term.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-04-03 20:26:59 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378386237  

Right.  
  
I sense I was misunderstood. I'm not suggesting to plan releases in long-term beyond 1.68.  
I'm suggesting to also track milestones using, if you will, the internal GIL versions.  
  
For instances, let's assume we don't know what next Boost version, we still know what is next Boost.GIL version. So, we can already assign PRs and issues to Boost.GIL version - that will also collect the release notes automagically.

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-04-03 20:28:22 UTC  
> Updated_at: 2018-04-03 20:29:48 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378386811  

As I have just checked above, there is no way to assign multiple milestones.  
So, if we want to track Boost.GIL version along Boost release version for issues and PRs, we would have to use milestones for the one and label for the other.  
  
I just really don't like going through the `git log` to fish out what has changed to collect release notes. But, if you are fine with it, I'm good too.

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2018-04-03 20:30:45 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378387825  

Right. I think we should stick to Boost versions if we want to sync with releases, but use our own milestones (such as in "Grand Merge") for anything else. As you mentioned elsewhere, I think it's a good idea to plan in terms of Boost releases, as that are the points we need to be aware of in terms of branch stability.

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-04-03 20:36:21 UTC  
> Url: https://github.com/boostorg/gil/pull/70#issuecomment-378390103  

OK. I like to tag milestones in terms of version numbers.

---
