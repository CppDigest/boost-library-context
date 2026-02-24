# #480 swap [Closed]

> Username: alandefreitas  
> Created at: 2022-08-31 00:19:43 UTC  
> Updated at: 2022-09-19 21:34:18 UTC  
> Closed at: 2022-09-01 22:56:39 UTC  
> Url: https://github.com/boostorg/url/pull/480  

fix #422, fix #468

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-31 00:34:45 UTC  
> Updated_at: 2022-09-01 22:41:19 UTC  
> Url: https://github.com/boostorg/url/pull/480#issuecomment-1232313062  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#480](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9aa8e83) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/8478ace01aab41bcc26443018ebafa2f13761888?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8478ace) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/480/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #480   +/-   ##  
========================================  
  Coverage    94.37%   94.38%             
========================================  
  Files          137      137             
  Lines         6577     6586    +9       
========================================  
+ Hits          6207     6216    +9       
  Misses         370      370             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/static\_url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zdGF0aWNfdXJsLmlwcA==) | `91.30% <ø> (ø)` | |  
| [include/boost/url/static\_url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `95.94% <100.00%> (+0.35%)` | :arrow_up: |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [8478ace...9aa8e83](https://codecov.io/gh/CPPAlliance/url/pull/480?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:11:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092661021  

📁 include/boost/url/static_url.hpp

```diff
  79 |+ 
  80 |+         @par Complexity
  81 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:11:39 UTC  
> Updated_at: 2022-09-01 01:11:46 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960138289  

delete this blank line


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:11:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092661106  

📁 include/boost/url/static_url.hpp

```diff
  83 |+ 
  84 |+         @par Exception Safety
  85 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:11:51 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960138365  

delete this blank line


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:12:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092661179  

📁 include/boost/url/static_url.hpp

```diff
  77 |+         linear time with no possibility of exceptions.
  78 |+         All views, iterators and references are invalidated.
  79 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:12:00 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960138410  

Missing `@par Example`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:13:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092661999  

📁 include/boost/url/static_url.hpp

```diff
  86 |+         Throws nothing.
  87 |+ 
  88 |+         @param other The static_url to swap with
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:13:44 UTC  
> Updated_at: 2022-09-01 01:13:55 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960139006  

You dont have to say "the static_url" just say "The object to swap with." The more we can cut and paste text the better.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:14:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092662268  

📁 include/boost/url/static_url.hpp

```diff
 265 |+         If `&lhs == &rhs`, this function call has no effect.
 266 |+ 
 267 |+         @see @ref static_url::swap
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:14:18 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960139187  

see refs each have their own line


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:14:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092662386  

📁 include/boost/url/static_url.hpp

```diff
 263 |+         @param rhs The static_url to exchange.
 264 |+ 
 265 |+         If `&lhs == &rhs`, this function call has no effect.
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:14:31 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960139264  

This statement should go up in the description


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:15:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092662730  

📁 include/boost/url/url.hpp

```diff
 267 |+         @param lhs The url to exchange.
 268 |+ 
 269 |+         @param rhs The url to exchange.
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:15:14 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960139550  

You can write  
```  
@param v0, v1 The objects to swap  
```


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:15:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092662974  

📁 include/boost/url/url.hpp

```diff
 245 |+     swap(url& other) noexcept;
 246 |+ 
 247 |+     /** Exchange the given urls.
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:15:46 UTC  
> Updated_at: 2022-09-01 01:15:47 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960139739  

"Exchange" is not a word, we say swap when we want to say swap. How about this:  
```  
/** Swap  
```


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 01:16:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1092663316  

📁 include/boost/url/url.hpp

```diff
 275 |+     friend
 276 |+     void
 277 |+     swap(url& lhs, url& rhs) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-09-01 01:16:27 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r960139963  

When you swap there is no notion of left or right, they just swap that's why I try to write `v0` and `v1`

> Username: alandefreitas  
> Created_at: 2022-09-01 21:15:49 UTC  
> Updated_at: 2022-09-01 21:15:50 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r961108237  

Oh... I was in doubt so I got this from boost.json. No problem.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-01 22:04:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/480#pullrequestreview-1094113901  

📁 include/boost/url/static_url.hpp

```diff
  76 |+         Exchanges the contents of this static_url with another
  77 |+         static_url. The contents of the urls are swapped in
  78 |+         linear time with no possibility of exceptions.
```

> Username: vinniefalco  
> Created_at: 2022-09-01 22:04:03 UTC  
> Url: https://github.com/boostorg/url/pull/480#discussion_r961139044  

This should be part of the Complexity and Exception Safety sections


---
