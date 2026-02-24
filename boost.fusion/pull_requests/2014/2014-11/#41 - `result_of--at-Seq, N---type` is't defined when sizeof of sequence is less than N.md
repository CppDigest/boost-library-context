# #41 `result_of::at<Seq, N>::type` is't defined when sizeof of sequence is less than N [Merged]

> Username: Flast  
> Created at: 2014-11-18 17:28:33 UTC  
> Updated at: 2014-11-19 01:47:05 UTC  
> Merged at: 2014-11-18 22:33:04 UTC  
> Closed at: 2014-11-18 22:33:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/41  

Split up from #39   
- Fix Out-of-Bounds sequence acessing via at and at_c.  
  - `result_of::at_c<sequence<int, char>, 2>::type` (and counterpart of `at`) doesn't occur hard error (static assert), but doesn't compile (SFINAE-friendly).  
  - IMO, SFINAE-friendly is better for consistency with any other `result_of` implementations.  
  
close [#6507](https://svn.boost.org/trac/boost/ticket/6507), [#7651](https://svn.boost.org/trac/boost/ticket/7651)

---

## Comment 1

> Username: djowel  
> Created_at: 2014-11-18 22:30:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/41#issuecomment-63557931  

Good! I like SFINAE-friendly strategy for this. Now I understand what you mean.

---
