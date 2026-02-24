# #603 edit_segments / any_segments refactor [Merged]

> Username: vinniefalco  
> Created at: 2022-10-17 16:53:22 UTC  
> Updated at: 2022-10-29 20:19:56 UTC  
> Merged at: 2022-10-17 22:51:10 UTC  
> Closed at: 2022-10-17 22:51:10 UTC  
> Url: https://github.com/boostorg/url/pull/603  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-17 17:03:41 UTC  
> Url: https://github.com/boostorg/url/pull/603#issuecomment-1281179261  

GCOVR code coverage report [https://603.url.prtest.cppalliance.org/gcovr/index.html](https://603.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://603.url.prtest.cppalliance.org/genhtml/index.html](https://603.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 2 [Commented]

> Username: alandefreitas  
> Created_at: 2022-10-17 17:26:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/603#pullrequestreview-1144547836  

I'm not sure I understand what this PR does

📁 include/boost/url/detail/any_segments_iter.hpp

```diff
  45 |+     // 1 = one
  46 |+     // 2 = two, or more
  47 |+     std::size_t fast_nseg = 0;
```

> Username: alandefreitas  
> Created_at: 2022-10-17 17:21:17 UTC  
> Updated_at: 2022-10-17 17:26:25 UTC  
> Url: https://github.com/boostorg/url/pull/603#discussion_r997325930  

Why do we need `std::size_t` for 3 values?

> Username: vinniefalco  
> Created_at: 2022-10-17 17:28:34 UTC  
> Url: https://github.com/boostorg/url/pull/603#discussion_r997332214  

you can make it smaller if you want, but it won't change the size of the struct. an int would be good


📁 include/boost/url/detail/impl/any_segments_iter.ipp

```diff
  85 |             if(*p == '/')
  86 |+             {
  87 |+                 ++fast_nseg;
```

> Username: alandefreitas  
> Created_at: 2022-10-17 17:24:09 UTC  
> Updated_at: 2022-10-17 21:36:08 UTC  
> Url: https://github.com/boostorg/url/pull/603#discussion_r997328338  

So this can go above 2? I'm confused now.

> Username: vinniefalco  
> Created_at: 2022-10-17 17:28:50 UTC  
> Updated_at: 2022-10-17 17:28:51 UTC  
> Url: https://github.com/boostorg/url/pull/603#discussion_r997332422  

it can't, because it breaks

---

📁 include/boost/url/detail/impl/any_segments_iter.ipp

```diff
 207 | {
 208 |     front = s;
 209 |+     fast_nseg = 1;
```

> Username: alandefreitas  
> Created_at: 2022-10-17 17:24:47 UTC  
> Updated_at: 2022-10-17 17:26:25 UTC  
> Url: https://github.com/boostorg/url/pull/603#discussion_r997328901  

Even for empty strings?

> Username: vinniefalco  
> Created_at: 2022-10-17 17:29:18 UTC  
> Updated_at: 2022-10-17 17:29:19 UTC  
> Url: https://github.com/boostorg/url/pull/603#discussion_r997332837  

Yes, because `segment_iter` is for exactly one segment (see where it is used)


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-17 18:40:27 UTC  
> Url: https://github.com/boostorg/url/pull/603#issuecomment-1281314661  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#603](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a46ba75) into [develop](https://codecov.io/gh/boostorg/url/commit/316e0d5a5f158e7d09ec2283735095f887cdfe2d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (316e0d5) will **decrease** coverage by `0.00%`.  
> The diff coverage is `98.03%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/603/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #603      +/-   ##  
===========================================  
- Coverage    96.68%   96.67%   -0.01%       
===========================================  
  Files          137      137                
  Lines         6600     6626      +26       
===========================================  
+ Hits          6381     6406      +25       
- Misses         219      220       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/603/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.18% <90.90%> (-1.82%)` | :arrow_down: |  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/603/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/603/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.03% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [316e0d5...a46ba75](https://codecov.io/gh/boostorg/url/pull/603?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
