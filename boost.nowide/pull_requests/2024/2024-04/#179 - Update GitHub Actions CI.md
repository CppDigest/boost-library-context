# #179 Update GitHub Actions CI [Merged]

> Username: striezel  
> Created at: 2024-04-30 21:21:59 UTC  
> Updated at: 2024-05-05 15:08:19 UTC  
> Merged at: 2024-05-03 15:03:24 UTC  
> Closed at: 2024-05-03 15:03:24 UTC  
> Url: https://github.com/boostorg/nowide/pull/179  

The following updates are performed:  
* update [`actions/cache`](https://github.com/actions/cache) to v4  
* update [`actions/checkout`](https://github.com/actions/checkout) to v4  
  
Still using the outdated actions will generate several warnings in CI runs, for example in https://github.com/boostorg/nowide/actions/runs/8432630230:  
  
> Node.js 16 actions are deprecated. Please update the following actions to use Node.js 20: actions/checkout@v3, actions/cache@v3. For more information see: https://github.blog/changelog/2023-09-22-github-actions-transitioning-from-node-16-to-node-20/.  
  
The PR will get rid of those warnings, because the newer versions of those actions use Node.js 20.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2024-05-02 07:14:21 UTC  
> Url: https://github.com/boostorg/nowide/pull/179#issuecomment-2089773507  

I'm afraid the container runs will fail as node 20 isn't available in Ubuntu 16 & 18, there is a workaround in Boost.CI: https://github.com/boostorg/boost-ci/blob/ed7ead8ae9b31b7f523850b0a0debd255a62f0cf/.github/workflows/ci.yml#L145-L147

---

## Comment 2

> Username: striezel  
> Created_at: 2024-05-03 08:46:56 UTC  
> Url: https://github.com/boostorg/nowide/pull/179#issuecomment-2092575551  

OK. I've added the workaround. Let's see whether this works here, too.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-05-03 12:40:13 UTC  
> Url: https://github.com/boostorg/nowide/pull/179#issuecomment-2092937836  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/179?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.45%. Comparing base [(`9d35cc4`)](https://app.codecov.io/gh/boostorg/nowide/commit/9d35cc47704a00b860cce60dc392fc955c1a9355?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dd1a4f1`)](https://app.codecov.io/gh/boostorg/nowide/pull/179?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #179      +/-   ##  
===========================================  
+ Coverage    97.72%   99.45%   +1.73%       
===========================================  
  Files           34       34                
  Lines         3034     3319     +285       
===========================================  
+ Hits          2965     3301     +336       
+ Misses          69       18      -51       
```  
  
[see 31 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/nowide/pull/179/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
</details>

---

## Comment 4

> Username: Flamefire  
> Created_at: 2024-05-03 15:03:19 UTC  
> Url: https://github.com/boostorg/nowide/pull/179#issuecomment-2093201973  

Ok, makes the file and output a bit more ugly but hopefully can be removed at some point. Thanks!

---
