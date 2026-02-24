# #1207 Make sure we don't activate float128 use when explicitly disabled [Merged]

> Username: mborland  
> Created at: 2024-10-10 13:13:31 UTC  
> Updated at: 2024-10-10 20:18:51 UTC  
> Merged at: 2024-10-10 16:44:44 UTC  
> Closed at: 2024-10-10 16:44:44 UTC  
> Url: https://github.com/boostorg/math/pull/1207  

@kursatyurt can you please give this patch a go and see if it solves your issue? If not can you please add a minimal reproducer and toolchain used to your issue. Thanks

---

## Comment 1

> Username: kursatyurt  
> Created_at: 2024-10-10 13:56:12 UTC  
> Url: https://github.com/boostorg/math/pull/1207#issuecomment-2405168379  

@mborland Thanks, this solves the issue. I will write a reproducer if I can find time.   
I am using `nvcc 12.2`. It was in a more complex project with Kokkos; hopefully, I can write a minimal example.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-10-10 13:59:39 UTC  
> Url: https://github.com/boostorg/math/pull/1207#issuecomment-2405177323  

> @mborland Thanks, this solves the issue. I will write a reproducer if I can find time. I am using `nvcc 12.2`. It was in a more complex project with Kokkos; hopefully, I can write a minimal example.  
  
Sounds good. FYI since the last release much of the library now supports running on device with NVCC and NVRTC.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-10-10 14:12:16 UTC  
> Updated_at: 2024-10-10 20:12:36 UTC  
> Url: https://github.com/boostorg/math/pull/1207#issuecomment-2405209883  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1207?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.78%. Comparing base [(`260f760`)](https://app.codecov.io/gh/boostorg/math/commit/260f7600e95dcf87b228d46a4a37cf594df5437b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a6afa23`)](https://app.codecov.io/gh/boostorg/math/commit/a6afa23eb2c957d3930b87693669241a72444cc9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1207/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1207?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1207   +/-   ##  
========================================  
  Coverage    93.78%   93.78%             
========================================  
  Files          657      657             
  Lines        53903    53903             
========================================  
  Hits         50555    50555             
  Misses        3348     3348             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1207?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1207?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [260f760...a6afa23](https://app.codecov.io/gh/boostorg/math/pull/1207?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-10-10 16:21:30 UTC  
> Url: https://github.com/boostorg/math/pull/1207#issuecomment-2405549109  

Thanks guys, looks good to me!

---
