# #122 Force inline boost::gregorian::date::date(special_values) to improve performance [Closed]

> Username: p12tic  
> Created at: 2020-02-03 21:26:10 UTC  
> Updated at: 2025-08-15 05:31:53 UTC  
> Closed at: 2025-08-15 05:31:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/122  

Fixes #121   
  
A small overview of benefits and potential downsides is extracted from #121 below:  
  
This compiler workaround would improve the code size and speed for the majority of user workloads that are affected.  
  
The only possibility this could be a regression would be in rarely executed call sites that pass non-constant value of special_values directly or indirectly to boost::gregorian::date::date. However, forcing inlining in such a case would just inflate code size a little bit and I suspect that the library user would still save code size overall due to significant reductions of code size around calls to default constructor of ptime and related types.  
  
In my case the performance of whole application has been improved by around 5%.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-04 01:29:11 UTC  
> Updated_at: 2025-08-15 05:31:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-581699751  

## [Codecov](https://app.codecov.io/gh/boostorg/date_time/pull/122?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 53.33%. Comparing base ([`061aec8`](https://app.codecov.io/gh/boostorg/date_time/commit/061aec856d4fdb2b53cc17cfa565b4e1c923b62a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`0c7c332`](https://app.codecov.io/gh/boostorg/date_time/commit/0c7c332787b9849e2c1c7eeb56d8e7874d19cb9b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 64 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/date_time/pull/122/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/date_time/pull/122?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #122      +/-   ##  
===========================================  
- Coverage    53.41%   53.33%   -0.09%       
===========================================  
  Files           83       83                
  Lines         4944     4933      -11       
  Branches      2407     2397      -10       
===========================================  
- Hits          2641     2631      -10       
  Misses         420      420                
+ Partials      1883     1882       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/date_time/pull/122?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/gregorian/greg\_date.hpp](https://app.codecov.io/gh/boostorg/date_time/pull/122?src=pr&el=tree&filepath=include%2Fboost%2Fdate_time%2Fgregorian%2Fgreg_date.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZGF0ZS5ocHA=) | `59.45% <100.00%> (-8.11%)` | :arrow_down: |  
  
... and [6 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/date_time/pull/122/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/date_time/pull/122?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/date_time/pull/122?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [061aec8...0c7c332](https://app.codecov.io/gh/boostorg/date_time/pull/122?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2020-02-11 05:46:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/122#pullrequestreview-356426661  

So as much as I can see using the FORCEINLINE, I'm wondering if with this function we shouldn't just write good old 'inline' -- I suspect that will work fine on all modern compilers and is more pleasing that yet another macro.

---

## Comment 3

> Username: p12tic  
> Created_at: 2020-02-11 10:57:23 UTC  
> Updated_at: 2020-02-11 10:57:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-584579782  

This function is already `inline` by being defined within the body of the class definition. `inline` does not tell the compiler anything about the actual inlining behaviour, it just makes the linker accept multiple definitions of the function. Thus `BOOST_FORCEINLINE` is essentially the only option.  
  
To verify in this particular case, I've tested `inline` on GCC 7.4.0 and it doesn't force inlining.

---

## Comment 4

> Username: p12tic  
> Created_at: 2020-03-01 12:35:29 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-593092122  

@JeffGarland Ping. Issues like this is what created bad opinion about Boost libraries in certain circles where performance is important :(

---

## Comment 5

> Username: JeffGarland  
> Created_at: 2020-03-02 02:14:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-593187559  

I really don't want to have a bad attitude on this change, but I'm not convinced it's the best way -- force inline seems like a non-standard hack -- I'd like to get out of that business. See my comment in the other issue about constexpr seems like that's the correct approach.   
  
Oh, I could really care less about what the performance oriented folks want -- I work for free on my spare time -- which means not on anyone else's timeline, sorry.  If people really want performance they are free to hack the code as you have and otherwise.

---

## Comment 6

> Username: p12tic  
> Created_at: 2020-03-02 11:49:17 UTC  
> Updated_at: 2020-03-02 12:09:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-593364755  

I'm sorry if I made it look like I expect free work from you, that's not the case. My point was that these issues are important to a certain subset of developers.  
  
Regarding constexpr, I agree that it would be the best solution. Unfortunately it does not seem to be enough in this case. I marked all functions involved in the constructor code path as `constexpr` and I still see the same problem.   
  
I believe that if there turn out to be no better alternatives, BOOST_FORCEINLINE would be justified to apply in this case. It speeds up date_time library as a whole several times. There are already more than 2 thousands uses of BOOST_FORCEINLINE throughout the boost libraries. The date_time library itself already uses plenty of per-compiler code paths.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2020-03-02 14:46:35 UTC  
> Updated_at: 2020-03-02 14:47:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-593437442  

Of course you want me to do work :)  I was only triggered by: "Issues like this is what created bad opinion...".  I'm sure no matter what I did someone won't be happy.  Some have moved to Howard's library bc the fundamental design (no special values) makes things faster.  
  
Do you have the changes with the constexpr on a branch somewhere - I'd be curious to see that.  Also, you're testing mostly with g++7.4?  Presumably with at least std=c++14?

---

## Comment 8

> Username: jeking3  
> Created_at: 2020-09-30 17:10:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-701523948  

Quite interesting to see the effect on code coverage from that one change...

---

## Comment 9

> Username: p12tic  
> Created_at: 2022-07-31 12:30:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/122#issuecomment-1200415709  

PR #214 is a better solution.

---
