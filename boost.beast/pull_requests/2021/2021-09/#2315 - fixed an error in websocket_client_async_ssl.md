# #2315 fixed an error in websocket_client_async_ssl [Closed]

> Username: ShuangLiu1992  
> Created at: 2021-09-20 15:36:12 UTC  
> Updated at: 2021-11-03 21:42:44 UTC  
> Closed at: 2021-11-03 21:42:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2315  

in this example host_ string should be updated after SSL_set_tlsext_host_name just like the synced version, otherwise this would cause a handshake error

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-09-20 16:10:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2315#issuecomment-923069807  

![pullrequest](https://2315.beast.tracing.cppalliance.org/pullrequest-884f9f8a.png)  
Timeline tracing charts: [https://2315.beast.tracing.cppalliance.org/index.html](https://2315.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-09-20 16:34:49 UTC  
> Updated_at: 2021-09-20 16:40:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2315#issuecomment-923089612  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2315?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2315](https://codecov.io/gh/boostorg/beast/pull/2315?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e53bb56) into [develop](https://codecov.io/gh/boostorg/beast/commit/b15a5ff0e47e72ba3d4711d2514bc65749d036ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15a5ff) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2315/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2315?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2315   +/-   ##  
========================================  
  Coverage    94.88%   94.88%             
========================================  
  Files          151      151             
  Lines        12230    12230             
========================================  
  Hits         11605    11605             
  Misses         625      625             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2315?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2315?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b15a5ff...e53bb56](https://codecov.io/gh/boostorg/beast/pull/2315?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 3 [Approved]

> Username: madmongo1  
> Created_at: 2021-09-25 20:18:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/2315#pullrequestreview-763627625  

Yes, this looks correct.

---
