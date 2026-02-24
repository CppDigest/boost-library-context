# #613 add testcase from https://oss-fuzz.com/testcase-detail/6157345913700352 [Closed]

> Username: pauldreik  
> Created at: 2021-08-18 18:10:02 UTC  
> Updated at: 2021-08-19 04:28:50 UTC  
> Closed at: 2021-08-19 04:28:50 UTC  
> Url: https://github.com/boostorg/json/pull/613  

this adds the testcase for reproducing #612   
  
to reproduce it locally, run fuzzing/fuzz.sh  
  
once the fuzzers are built, you can run it directly through   
```sh  
fuzzing/fuzzer_parser fuzzing/old_crashes/parser/clusterfuzz-testcase-minimized-fuzz_parser-6157345913700352  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-08-18 18:32:15 UTC  
> Url: https://github.com/boostorg/json/pull/613#issuecomment-901338114  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/613?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#613](https://codecov.io/gh/boostorg/json/pull/613?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8b0a3bb) into [develop](https://codecov.io/gh/boostorg/json/commit/3f48a274589aac06280b2c1b0e3d70796cac4b05?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3f48a27) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 8b0a3bb differs from pull request most recent head be3514c. Consider uploading reports for the commit be3514c to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/613/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/613?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #613   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6114     6114             
========================================  
  Hits          6056     6056             
  Misses          58       58             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/613?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/613?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3f48a27...be3514c](https://codecov.io/gh/boostorg/json/pull/613?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: pauldreik  
> Created_at: 2021-08-19 04:28:50 UTC  
> Url: https://github.com/boostorg/json/pull/613#issuecomment-901600624  

this is obsoleted by #614 which adds not only this test case but also another.

---
