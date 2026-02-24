# #2336 Move library-specific docca configuration to Beast [Closed]

> Username: evanlenz  
> Created at: 2021-10-31 04:46:25 UTC  
> Updated at: 2021-11-03 21:42:43 UTC  
> Closed at: 2021-11-03 21:42:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2336  

relates to boostorg/docca#107

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-10-31 04:56:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2336#issuecomment-955637188  

An automated preview of the documentation is available at [https://2336.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2336.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-10-31 05:39:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2336#pullrequestreview-793668426  

📁 doc/xsl/custom-overrides.xsl

```diff
  23 |+     'Body',
  24 |+     'BufferSequence',
  25 |+     'BufferSequence',  (: TODO: Was this intended to be 'BufferSequence_' ?? :)
```

> Username: vinniefalco  
> Created_at: 2021-10-31 05:39:25 UTC  
> Updated_at: 2021-10-31 05:39:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2336#discussion_r739760116  

Yeah probably


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2021-10-31 05:41:26 UTC  
> Updated_at: 2021-10-31 06:04:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2336#issuecomment-955640707  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2336](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6c3c4bc) into [develop](https://codecov.io/gh/boostorg/beast/commit/b15a5ff0e47e72ba3d4711d2514bc65749d036ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15a5ff) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2336/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2336      +/-   ##  
===========================================  
- Coverage    94.88%   94.85%   -0.04%       
===========================================  
  Files          151      151                
  Lines        12230    12230                
===========================================  
- Hits         11605    11601       -4       
- Misses         625      629       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2336/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b15a5ff...6c3c4bc](https://codecov.io/gh/boostorg/beast/pull/2336?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
