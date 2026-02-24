# #587 Prelim fix issue586 (if none better found) [Merged]

> Username: ckormanyos  
> Created at: 2024-01-22 18:16:42 UTC  
> Updated at: 2024-01-23 17:08:00 UTC  
> Merged at: 2024-01-23 17:05:01 UTC  
> Closed at: 2024-01-23 17:05:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587  

The purpose of this PR is to provide a minimalistic fix to #586.  
  
In that issue, we see a phenomenon that may require conversion to number-wrap or non-wrapped type detected in overloads of `trunc` and `lltrunc`.  
  
If no better fix is found in this releasce cycle, then this _does the trick_.  
  
Cc: @jzmaddock John take a look if you find better...  
  
Cc: @mborland Standalone with/without the presence of `Math`.  
  
Cc: @radj307

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-01-23 06:09:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1905352439  

It's green-ish with CodeCov upload error (re-running) and 1 timeout on drone.  
  
Hi @jzmaddock do you think the potential fix in this PR is sufficient?  
  
Cc: @mborland

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-01-23 10:54:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1905789587  

I'll take a look shortly.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-01-23 12:28:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1905958582  

That's fine, a simpler fix is:  
  
```  
    if (arg > 0)   
       return floor(arg);  
    return ceil(arg);  
```  
  
which doesn't then require a temporary.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-01-23 12:33:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1905966788  

> That's fine, a simpler fix is:  
  
Thank you John. It is now cycling in CI.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2024-01-23 14:07:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1906133855  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `1 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`878138a`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/878138ad145254c95ada46203505036c1d8d55f4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 94.1% compared to head [(`29ca08c`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 84.6%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/587/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #587      +/-   ##  
==========================================  
- Coverage     94.1%   84.6%    -9.5%       
==========================================  
  Files          273     149     -124       
  Lines        28523   16332   -12191       
==========================================  
- Hits         26833   13805   -13028       
- Misses        1690    2527     +837       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/multiprecision/detail/functions/trunc.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL3RydW5jLmhwcA==) | `84.7% <66.7%> (-6.2%)` | :arrow_down: |  
  
... and [159 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/587/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [878138a...29ca08c](https://app.codecov.io/gh/boostorg/multiprecision/pull/587?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-01-23 17:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1906521218  

Hi Matt (@mborland) in Multiprecision, uploading Coverage from suite number 1 takes too long and times out all the time, or a lot of the time. We need to eventually look into this.  
  
I can help when we both have a time slot for this...  
  
Cc: @jzmaddock

---

## Comment 7

> Username: mborland  
> Created_at: 2024-01-23 17:05:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1906525652  

In math every test suite gets its own coverage run because just doing suite 1 and suite 2 also had issues with timeouts. Porting that logic is likely the easiest way to solve this.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-01-23 17:07:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/587#issuecomment-1906531875  

> every test suite gets its own coverage run  
  
I appreciate that. Many thanks @mborland

---
