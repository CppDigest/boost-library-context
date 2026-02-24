# #197 Update GitHub Actions CI file [Closed]

> Username: sdarwin  
> Created at: 2021-08-02 19:08:42 UTC  
> Updated at: 2022-02-10 15:36:50 UTC  
> Closed at: 2022-02-10 15:36:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/197  

The Ubuntu 16.04 environment is scheduled to be removed from GitHub Actions in September 2021. Migrate those jobs to Docker containers or Ubuntu 18.04. Also, fix a problem with pip package installation on older platforms.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-08-02 20:11:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/197#issuecomment-891300641  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/197?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#197](https://codecov.io/gh/boostorg/date_time/pull/197?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d5447b8) into [develop](https://codecov.io/gh/boostorg/date_time/commit/7156392706faa282044ce9f1d6ba9fdd207f6da9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7156392) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head d5447b8 differs from pull request most recent head c75517b. Consider uploading reports for the commit c75517b to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/197/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/date_time/pull/197?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #197   +/-   ##  
========================================  
  Coverage    52.82%   52.82%             
========================================  
  Files           76       76             
  Lines         4483     4483             
  Branches      2239     2239             
========================================  
  Hits          2368     2368             
  Misses         391      391             
  Partials      1724     1724             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/197?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/197?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7156392...c75517b](https://codecov.io/gh/boostorg/date_time/pull/197?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: jeking3  
> Created_at: 2022-02-10 15:30:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/197#issuecomment-1035057613  

I am realigning CI to boost-ci current practices, in the feature/ci branch.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2022-02-10 15:36:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/197#issuecomment-1035064888  

If you are making the changes in feature/ci, then I'll close this PR.

---
