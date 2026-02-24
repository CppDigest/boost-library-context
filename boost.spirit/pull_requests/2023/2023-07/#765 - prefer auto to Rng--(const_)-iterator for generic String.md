# #765 prefer auto to Rng::(const_)?iterator for generic String [Closed]

> Username: wanghan02  
> Created at: 2023-07-20 10:32:13 UTC  
> Updated at: 2025-05-09 08:39:15 UTC  
> Closed at: 2025-05-09 08:39:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/765  

1. c++20 ranges library does not require a range to have member type/alias iterator/const_iterator.  
2. boost range library uses `typename boost::range_iterator<Rng>::type` instead of `Rng::iterator` because a customized Rng may not have member type/alias iterator/const_iterator. e.g. `std::pair<It, It>`.  
  
Therefore for generic String type, `auto` is better than `Rng::(const_)?iterator`.

---

## Comment 1

> Username: wanghan02  
> Created_at: 2023-08-16 12:31:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/765#issuecomment-1680517695  

The changes in this commit are all in x3 folder with std14 at least. Somehow CI fails in some std03/11 karma/qi builds.

---

## Comment 2

> Username: saki7  
> Created_at: 2025-05-09 08:39:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/765#issuecomment-2865680737  

I'm afraid that this is not the ideal solution for upgrading the codebase. For instance, C++20 ranges have `std::ranges::iterator_t` which is advised to be used when extracting such traits.  
  
Also, a quick search on `x3/` directory shows that we have more non-trivial issues regarding the legacy/modern mismatch of range handlings, such as: https://github.com/boostorg/spirit/blob/ba53544ac90778a7f2774c21fb552572bf7b37bd/include/boost/spirit/home/x3/support/traits/is_range.hpp#L16-L24  
  
If we are to do the modernization on range manipulations, we should apply the fix comprehensively. This PR might surely solve some issues, but a lot more work should be done (sadly).  
  
Feel free to reopen if you have a better implementation.

---
