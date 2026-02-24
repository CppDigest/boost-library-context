# #130 params assignment [Closed]

> Username: alandefreitas  
> Created at: 2022-02-24 14:26:24 UTC  
> Updated at: 2022-03-14 15:56:22 UTC  
> Closed at: 2022-02-25 00:46:38 UTC  
> Url: https://github.com/boostorg/url/pull/130  

Fixes #77   
  
This PR prevents rvalue assignment from compiling:  
  
```cpp  
u1.params() = u2.params();  
```  
  
while still allowing lvalue assignment to compile:  
  
```cpp  
auto p = u1.params();  
p = u2.params();  
```

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-24 14:50:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/130#pullrequestreview-892547225  

📁 include/boost/url/params.hpp

```diff
 352 |     empty() const noexcept;
 353 | 
 354 |+     /** Determine the container size
```

> Username: vinniefalco  
> Created_at: 2022-02-24 14:50:56 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r813955342  

"Return the number of elements"

> Username: vinniefalco  
> Created_at: 2022-02-24 14:51:29 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r813955868  

The briefs for functions which return values usually start with the word "Return."

> Username: vinniefalco  
> Created_at: 2022-02-24 16:40:18 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814066295  

This isn't resolved, it should read:  
```  
/** Return the number of elements  
...  
```

> Username: alandefreitas  
> Created_at: 2022-02-24 17:49:19 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814124367  

This preview is outdated.  
  
https://github.com/alandefreitas/url/blob/9594ab9b7c0e9151440a1310cffbfe9806fe55d7/include/boost/url/params.hpp#L349


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-24 14:56:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/130#pullrequestreview-892556080  

📁 include/boost/url/params.hpp

```diff
 174 |+ 
 175 |+      */
 176 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-02-24 14:56:58 UTC  
> Updated_at: 2022-02-24 14:56:59 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r813961603  

I thought we were putting `inline` on the definitions and not the declarations?

> Username: alandefreitas  
> Created_at: 2022-02-24 16:34:35 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814060557  

There's no definition because the declaration is just `= default`.

> Username: vinniefalco  
> Created_at: 2022-02-24 16:39:44 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814065793  

Oh, yeah, didnt' see that. Then we don't need `inline` at all, because definitions which appear in the declaration are inline by default, and a defaulted member function counts as a definition.


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-02-24 15:29:13 UTC  
> Url: https://github.com/boostorg/url/pull/130#issuecomment-1049976360  

put `fix #77` in the body of the commit message and it will auto-close the issue

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-24 16:44:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/130#pullrequestreview-892710289  

📁 include/boost/url/params_encoded.hpp

```diff
  94 |+ 
  95 |+      */
  96 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-02-24 16:44:57 UTC  
> Updated_at: 2022-02-24 16:44:58 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814070543  

Don't need `inline` here


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-24 16:45:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/130#pullrequestreview-892710713  

📁 include/boost/url/params_encoded_view.hpp

```diff
 154 |+ 
 155 |+      */
 156 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-02-24 16:45:16 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814070815  

don't need inline here


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-24 17:09:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/130#pullrequestreview-892740647  

📁 test/unit/params.cpp

```diff
 508 |+         params u2p = u2.params();
 509 |+         u2p = u1p;
 510 |+         BOOST_TEST((*u2p.find("a")).value == "1");
```

> Username: vinniefalco  
> Created_at: 2022-02-24 17:09:06 UTC  
> Url: https://github.com/boostorg/url/pull/130#discussion_r814091428  

do the other container tests already check assignment?


---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2022-02-24 20:49:15 UTC  
> Url: https://github.com/boostorg/url/pull/130#issuecomment-1050250635  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#130](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9594ab9) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/674153089551371eb05901d615c1702e258eadfc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (6741530) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/130/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #130   +/-   ##  
========================================  
  Coverage    96.39%   96.39%             
========================================  
  Files          104      106    +2       
  Lines         5489     5492    +3       
========================================  
+ Hits          5291     5294    +3       
  Misses         198      198             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/query\_param.hpp](https://codecov.io/gh/CPPAlliance/url/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcXVlcnlfcGFyYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [6741530...9594ab9](https://codecov.io/gh/CPPAlliance/url/pull/130?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
