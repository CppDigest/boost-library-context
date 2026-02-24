# #708 More cover dec_float and improve comments [Merged]

> Username: ckormanyos  
> Created at: 2025-07-01 09:56:05 UTC  
> Updated at: 2025-07-01 17:21:00 UTC  
> Merged at: 2025-07-01 17:05:35 UTC  
> Closed at: 2025-07-01 17:05:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/708  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-01 11:47:56 UTC  
> Updated_at: 2025-07-01 17:21:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/708#issuecomment-3023614500  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.7%. Comparing base [(`2b04662`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/2b04662e41a9b55d043cacc65987a9c19ed51ffa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4a53f87`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/4a53f873972d448fdfe3c2eee9e363916bb0a91c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/708/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #708     +/-   ##  
=========================================  
+ Coverage     94.6%   94.7%   +0.2%       
=========================================  
  Files          295     296      +1       
  Lines        30728   30881    +153       
=========================================  
+ Hits         29044   29233    +189       
+ Misses        1684    1648     -36       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_dec_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | `96.8% <100.0%> (+2.7%)` | :arrow_up: |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <ø> (ø)` | |  
| [test/test\_various\_edges.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?src=pr&el=tree&filepath=test%2Ftest_various_edges.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZhcmlvdXNfZWRnZXMuY3Bw) | `100.0% <ø> (ø)` | |  
| [test/test\_various\_edges\_more.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?src=pr&el=tree&filepath=test%2Ftest_various_edges_more.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZhcmlvdXNfZWRnZXNfbW9yZS5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/708/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2b04662...4a53f87](https://app.codecov.io/gh/boostorg/multiprecision/pull/708?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-07-01 14:00:43 UTC  
> Updated_at: 2025-07-01 14:01:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/708#issuecomment-3024152866  

An actual bug has been found.  
  
In the division with `unsigned long long` function, we find the lines in the image below. Yes, division of $x/0$ will be infinite for finite $x$. But when setting the result to `inf`, the sign information is lost and must be retrieved from the cached value.  
  
![image](https://github.com/user-attachments/assets/1fc2a484-047d-4b99-a9db-0f3aaadf7d38)

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-07-01 17:05:39 UTC  
> Updated_at: 2025-07-01 17:06:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/708#issuecomment-3024863831  

OK so this was a productive, exciting and fun one.  
  
- We got a couple $0.1\\%$ more coverage.  
- Picked up a lot of edge-cases in `cpp_dec_float` and als `cpp_bin_float`.  
- I found a place I can strengthen `cpp_double_fp_backend` down the road. But it's not a showstopper in any way.  
  
So I'm going to commit this. We didn't _quite_  get up to $95.0\\%$ coverage, but not bad at $94.7\\%$, which is up from $94.1\\%$ compared to 1.88.  
  
Good progress.  
  
So for me, it's time to ramp down for 1.89. I'm done (or done in).  
  
Cc: @jzmaddock and @mborland

---
