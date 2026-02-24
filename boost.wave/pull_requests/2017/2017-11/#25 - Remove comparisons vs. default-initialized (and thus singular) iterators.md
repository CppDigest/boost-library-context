# #25 Remove comparisons vs. default-initialized (and thus singular) iterators [Merged]

> Username: jefftrull  
> Created at: 2017-11-30 02:34:00 UTC  
> Updated at: 2018-05-17 03:45:24 UTC  
> Merged at: 2017-11-30 13:43:26 UTC  
> Closed at: 2017-11-30 13:43:26 UTC  
> Url: https://github.com/boostorg/wave/pull/25  

MSVC's standard library has checked iterators which flagged that we compare default-initialized iterators against regular iterators, which is not allowed by the standard.  The purpose of those comparisons is to ensure the iterators (formerly strictly pointers) are initialized before dereferencing them.  
As it turns out, a constructor was added to the Scanner class as part of the changes that introduced this problem, and so the iterators will always be initialized, and we can remove these checks.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-30 02:46:06 UTC  
> Url: https://github.com/boostorg/wave/pull/25#issuecomment-348065964  

Hartmut, would you like me to add Travis support to this repo, so that the pull requests are tested?

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2017-11-30 13:38:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/25#pullrequestreview-80183054  

Thanks!

---
