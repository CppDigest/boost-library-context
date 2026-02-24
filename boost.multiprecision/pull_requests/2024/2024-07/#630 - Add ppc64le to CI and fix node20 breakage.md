# #630 Add ppc64le to CI and fix node20 breakage [Closed]

> Username: mborland  
> Created at: 2024-07-08 14:59:07 UTC  
> Updated at: 2025-06-25 12:46:37 UTC  
> Closed at: 2025-06-25 12:45:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-08 16:06:41 UTC  
> Updated_at: 2025-06-25 12:46:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630#issuecomment-2214558917  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/630?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`1a06c2e`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/1a06c2ee0c0ea23fa17fcb7f16894af06a6e21fd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`366a6bc`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/366a6bc66d8af2e81f5d06f6eae68b5b4720dc3e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 85 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/630/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/630?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #630   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          276     276             
  Lines        26846   26846             
=======================================  
  Hits         25240   25240             
  Misses        1606    1606             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/630?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/630?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1a06c2e...366a6bc](https://app.codecov.io/gh/boostorg/multiprecision/pull/630?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-07-09 17:40:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630#issuecomment-2218302153  

@jzmaddock, @ckormanyos, @NAThompson before I merge this in do you see adding PPC64LE to our CI as valuable? The tradeoff is that it increases the CI run time to about 4 hours. It's not a super high demand architecture but I know in math, charconv, and others there are occasional bug reports.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-07-10 09:13:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630#issuecomment-2219977689  

I guess it's main use is as a "different" architecture.  Other than that I'm guessing is basically obsolete now?

---

## Comment 4

> Username: mborland  
> Created_at: 2024-07-10 12:22:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630#issuecomment-2220375910  

> I guess it's main use is as a "different" architecture. Other than that I'm guessing is basically obsolete now?  
  
Yes it's little endian with two different 128-bit long double ABIs (__ibm128 and __float128) depending on which OS/compiler you are using. I don't think it's obsolete just really expensive. I got a quote for what it would cost to run natively in Drone like ARM and s390x, and it was prohibitive.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-07-11 11:42:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630#issuecomment-2222711059  

> Yes it's little endian with two different 128-bit long double ABIs (__ibm128 and __float128) depending on which OS/compiler you are using. I don't think it's obsolete just really expensive. I got a quote for what it would cost to run natively in Drone like ARM and s390x, and it was prohibitive.  
  
Understood.  Many many moon ago, when some hardware vendors provided "compiler farms" you could telnet into, I wondered if it wouldn't be possible to persuade some of them to donate some older machines to the project... but then I realised that someone would have to actually make space for them, put up with the noise and the heat, and feed them actual electrons!  
  
If we (or rather Vinnie) were going to spend actual $$'s, then I suspect providing a CUDA-enabled test machine might be the thing to spend it on.  It's also rather niche, but increasingly important IMO?

---

## Comment 6

> Username: mborland  
> Created_at: 2024-07-11 12:30:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/630#issuecomment-2222811601  

> > Yes it's little endian with two different 128-bit long double ABIs (__ibm128 and __float128) depending on which OS/compiler you are using. I don't think it's obsolete just really expensive. I got a quote for what it would cost to run natively in Drone like ARM and s390x, and it was prohibitive.  
>   
> Understood. Many many moon ago, when some hardware vendors provided "compiler farms" you could telnet into, I wondered if it wouldn't be possible to persuade some of them to donate some older machines to the project... but then I realised that someone would have to actually make space for them, put up with the noise and the heat, and feed them actual electrons!  
>   
> If we (or rather Vinnie) were going to spend actual $$'s, then I suspect providing a CUDA-enabled test machine might be the thing to spend it on. It's also rather niche, but increasingly important IMO?  
  
I can ask about some CUDA machines as that would be quite useful. You also can't emulate CUDA so it has to run native.

---
