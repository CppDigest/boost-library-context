# #618 make sure eval_convert_to() do not terminate with super large number [Merged]

> Username: tchaikov  
> Created at: 2024-05-03 07:20:23 UTC  
> Updated at: 2024-05-08 17:30:28 UTC  
> Merged at: 2024-05-04 16:46:20 UTC  
> Closed at: 2024-05-04 16:46:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618  

this change is a follow-up of d51f2e9dbb. it intends to address the exception thrown in a noexcept functon.  
  
a minimal reproducer looks like  
  
```c++  
  
int main() {  
    std::string s = "32767456456456456456545678943512357658768763546575675";  
    boost::multiprecision::cpp_int num(s);  
    std::cout << num.convert_to<float>() << std::endl;  
}  
```  
  
since boost 1.79, the code above terminates like  
```  
Program returned: 139  
Program stderr  
terminate called after throwing an instance of 'boost::wrapexcept<std::domain_error>'  
  what():  Error in function float_next<float>(float): Argument must be finite, but got inf  
Program terminated with signal: SIGSEGV  
```  
  
because `float_next_imp()` throws `boost::wrapexcept<std::domain_error>` if the number is NAN of INF. and `eval_convert_to()` is marked as `noexcept(boost::multiprecision::detail::is_arithmetic<R>::value &&  
          std::numeric_limits<R>::has_infinity)`,  
but only `overflow_error` is ignored in the policy passed to `float_next()`.  
  
so, in this change, `std::domain_error` is ignored as well, so that `num.convert_to<float>()` returns a NaN in this case.  
  
Refs #553

---

## Comment 1

> Username: tchaikov  
> Created_at: 2024-05-03 07:21:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2092450018  

a reproducer can be found at https://godbolt.org/z/K5nsj5azT

---

## Comment 2

> Username: tchaikov  
> Created_at: 2024-05-03 10:09:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2092703466  

hi @jzmaddock , could you help review this change? my colleague @nyh believes that an "inf" should be returned instead, as it was the behaviour in boost 1.78. what do you think?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-05-03 16:28:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2093349277  

It looks like it's fine: but your parenthesis are in the wrong place around make_policy, should be (I hope):  
  
```  
*result = boost::math::float_next(*result, boost::math::policies::make_policy(boost::math::policies::overflow_error<boost::math::policies::ignore_error>(),                                                                                             boost::math::policies::domain_error<boost::math::policies::ignore_error>()));  
```

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-05-03 16:30:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2093355717  

Never mind, I see I can edit, have changed thing round...

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2024-05-03 17:52:59 UTC  
> Updated_at: 2024-05-04 08:48:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2093496555  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`fe3054f`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/fe3054f4b8e54aa91b35a04a51fd2bd6f3c68f57?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ea78649`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/618/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #618     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          274     274               
  Lines        26765   26766      +1       
=========================================  
+ Hits         25162   25163      +1       
  Misses        1603    1603               
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/misc.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fmisc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L21pc2MuaHBw) | `91.5% <100.0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fe3054f...ea78649](https://app.codecov.io/gh/boostorg/multiprecision/pull/618?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-05-03 18:02:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2093508909  

Failures should be corrected by https://github.com/boostorg/math/pull/1130 once it runs...

---

## Comment 7

> Username: tchaikov  
> Created_at: 2024-05-04 01:03:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2093933431  

> Never mind, I see I can edit, have changed thing round...  
  
thank you! squashed and repushed.

---

## Comment 8

> Username: nyh  
> Created_at: 2024-05-08 11:30:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2100366033  

> hi @jzmaddock , could you help review this change? my colleague @nyh believes that an "inf" should be returned instead, as it was the behaviour in boost 1.78. what do you think?  
  
I stand by my belief that when converting an integer to floating-point, truncation should result in +Inf (or -Inf) and not NaN. Returning Inf in the case of truncation, and not NaN, is traditional, for example:  
```  
>>> print(1000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000.0)  
inf  
```  
but even more importantly, this also this was the behaviour in older Boost before this bug happened, and application code may rely on this truncation behavior for its own user-visible truncation (as for example happened in the ScyllaDB project).  
  
So although after this patch the conversion no longer crashes (good), it's still wrong in my view.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-05-08 17:04:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2101026012  

My bad, you're quite correct, see https://github.com/boostorg/math/issues/1132.

---

## Comment 10

> Username: nyh  
> Created_at: 2024-05-08 17:24:00 UTC  
> Updated_at: 2024-05-08 17:30:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/618#issuecomment-2101058412  

> My bad, you're quite correct, see [boostorg/math#1132](https://github.com/boostorg/math/issues/1132).  
  
Thanks. I'm not familiar with Boost's unit tests, but if you have them, I recommend that you add regression tests for convert_to() whose results are expected to be both +inf **and** -inf, because it's possible the details of what broke (float_nex()t? I didn't understand the details) would be different in the two cases. Although float_next(+inf) might perhaps be defined, what is float_next(-inf) supposed to return?  
  
UPDATE: I read the convert_to code again, and it seems it works on the positive number, and always adds the sign at the end - so it will always be +Inf, not -Inf, at this stage. So I think the code is indeed fine, and you just need float_next(+Inf) to return +Inf. Or, perhaps not call this function in the case of Inf?

---
