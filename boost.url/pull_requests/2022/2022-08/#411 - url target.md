# #411 url target [Merged]

> Username: alandefreitas  
> Created at: 2022-08-16 06:48:14 UTC  
> Updated at: 2022-08-30 21:03:54 UTC  
> Merged at: 2022-08-17 07:23:35 UTC  
> Closed at: 2022-08-17 07:23:35 UTC  
> Url: https://github.com/boostorg/url/pull/411  

fix #399

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-16 07:03:26 UTC  
> Updated_at: 2022-08-17 04:22:17 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1216229691  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#411](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (e570e42) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/68c53b6192805fe67bc723eff4c1a5ccdb8b3687?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (68c53b6) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/411/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #411   +/-   ##  
========================================  
  Coverage    97.89%   97.89%             
========================================  
  Files          132      132             
  Lines         6281     6285    +4       
========================================  
+ Hits          6149     6153    +4       
  Misses         132      132             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/411/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [68c53b6...e570e42](https://codecov.io/gh/CPPAlliance/url/pull/411?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-16 13:54:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/411#pullrequestreview-1074163225  

📁 test/unit/url_view_base.cpp

```diff
 310 |+         //----------------------------------------
 311 |+         //
 312 |+         // Resource
```

> Username: vinniefalco  
> Created_at: 2022-08-16 13:54:42 UTC  
> Url: https://github.com/boostorg/url/pull/411#discussion_r946809826  

uhhh don't add top level sections like this just put it with the other compound fields..

> Username: alandefreitas  
> Created_at: 2022-08-16 16:32:47 UTC  
> Url: https://github.com/boostorg/url/pull/411#discussion_r947005910  

Which one? Path?   
  
I couldn't find a good compound field to put that because this would be the compound field with path+query, but I don't think we want to remove path and query to have a single resource section.

> Username: vinniefalco  
> Created_at: 2022-08-16 17:05:48 UTC  
> Updated_at: 2022-08-16 17:05:49 UTC  
> Url: https://github.com/boostorg/url/pull/411#discussion_r947038405  

Make a new section below everything, without a name just give it a line to divide it


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-08-16 22:11:39 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1217222031  

What about the help card?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-08-16 22:25:57 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1217232466  

>  What about the help card?  
  
Oh... that's painful. Are we keeping these names (`target` and `resource`)?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-08-16 23:38:46 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1217277020  

I like the names. I can update the help card. Let me grab this branch and throw a commit on it

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-08-16 23:51:31 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1217284741  

HelpCard https://github.com/vinniefalco/url/commit/1d2406cdbd594cb56e37dc9eb9b8d24a9decf342

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-08-17 04:15:23 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1217441413  

An automated preview of the documentation is available at [https://411.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://411.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-08-17 04:41:57 UTC  
> Url: https://github.com/boostorg/url/pull/411#issuecomment-1217454475  

squash it and merge it when you think its ready

---
