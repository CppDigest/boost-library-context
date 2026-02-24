# #77 CI: Test only building the library  [Closed]

> Username: Flamefire  
> Created at: 2022-05-17 14:08:35 UTC  
> Updated at: 2022-05-24 13:24:55 UTC  
> Closed at: 2022-05-24 13:24:55 UTC  
> Url: https://github.com/boostorg/locale/pull/77  

Tests currently fail on various platforms due to assumptions on how the underlying backend works which may change, e.g. between versions.  
In order to have any CI at all disable the runtime tests for now.  
  
Requires:   
- [ ] #76

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-23 14:22:07 UTC  
> Updated_at: 2022-05-24 13:21:24 UTC  
> Url: https://github.com/boostorg/locale/pull/77#issuecomment-1134743113  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/77?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@9259845`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 3953317 differs from pull request most recent head 8def41c. Consider uploading reports for the commit 8def41c to get more accurate results  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #77   +/-   ##  
==========================================  
  Coverage           ?   80.07%             
==========================================  
  Files              ?       76             
  Lines              ?     5958             
  Branches           ?        0             
==========================================  
  Hits               ?     4771             
  Misses             ?     1187             
  Partials           ?        0             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/77?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/77?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9259845...8def41c](https://codecov.io/gh/boostorg/locale/pull/77?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
