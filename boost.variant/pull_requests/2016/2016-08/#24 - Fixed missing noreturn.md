# #24 Fixed missing noreturn [Merged]

> Username: Kojoley  
> Created at: 2016-08-31 18:18:36 UTC  
> Updated at: 2016-09-02 18:20:52 UTC  
> Merged at: 2016-08-31 18:56:16 UTC  
> Closed at: 2016-08-31 18:56:16 UTC  
> Url: https://github.com/boostorg/variant/pull/24  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-08-31 18:52:54 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-243863759  

Looks like all the `forced_return()` in that header must be marked with `BOOST_NORETURN`.  
  
It also looks like there's a misleading documentation for `BOOST_NORETURN` macro (`BOOST_NORETURN` is applicable to any function, not just to functions that return `void`)

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-08-31 18:54:52 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-243864361  

I have tried it and then clang warns about `return` statement in `noreturn` function.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-08-31 18:56:25 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-243864832  

Thanks for the patch!

---

## Comment 4

> Username: Kojoley  
> Created_at: 2016-09-01 19:20:46 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-244183891  

I've spotted opposite warning in the http://www.boost.org/development/tests/develop/output/GLIS-homo-impi-spirit-intel-linux-warnings.html#qi_utree1-p3 build. It seems related to the PR.  
  
```  
../boost/variant/detail/forced_return.hpp(50): warning #1628: function declared with "noreturn" does return  
```  
  
I think because of `--bjam-options=release` flag `BOOST_ASSERT` becomes nothing (if I correctly understand the doc http://www.boost.org/doc/libs/1_61_0/libs/assert/assert.html). Maybe it is better to change it to `BOOST_VERIFY` or `std::terminate`?

---

## Comment 5

> Username: apolukhin  
> Created_at: 2016-09-02 17:30:23 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-244438409  

std::terminate seems right. But it's better not to change assert, but rather add std::terminate after the assert.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2016-09-02 17:35:31 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-244439669  

But I'm afraid that it will produce some other warnings. Could you please check the solution with std::terminate after assert?

---

## Comment 7

> Username: Kojoley  
> Created_at: 2016-09-02 17:46:31 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-244442473  

Ok. Will be soon with results.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2016-09-02 18:14:43 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-244449587  

I can confirm that adding `std::terminate` at the end of the function (after the `BOOST_ASSERT`) solves the problem and do not produce other warnings. Checked with Clang (`-Wmissing-noreturn` flag) and `variant=debug,release`. Should I open a PR or you will it yourself?

---

## Comment 9

> Username: Kojoley  
> Created_at: 2016-09-02 18:19:06 UTC  
> Updated_at: 2016-09-02 18:20:52 UTC  
> Url: https://github.com/boostorg/variant/pull/24#issuecomment-244450617  

`-Wall -Wextra -Wpedantic -Wmissing-noreturn` is OK too, got only this:  
  
```  
auto_visitors.cpp:154:37: warning: unused parameter 'val' [-Wunused-parameter]  
    const char* operator()(const T& val) const {  
                                    ^  
auto_visitors.cpp:159:38: warning: unused parameter 'v1' [-Wunused-parameter]  
    const char* operator()(const T1& v1, const T2& v2) const {  
                                     ^  
auto_visitors.cpp:159:52: warning: unused parameter 'v2' [-Wunused-parameter]  
    const char* operator()(const T1& v1, const T2& v2) const {  
                                                   ^  
auto_visitors.cpp:165:38: warning: unused parameter 'v1' [-Wunused-parameter]  
    const char* operator()(const T1& v1, const T2& v2, const T3& v3) const {  
                                     ^  
auto_visitors.cpp:165:52: warning: unused parameter 'v2' [-Wunused-parameter]  
    const char* operator()(const T1& v1, const T2& v2, const T3& v3) const {  
                                                   ^  
auto_visitors.cpp:165:66: warning: unused parameter 'v3' [-Wunused-parameter]  
    const char* operator()(const T1& v1, const T2& v2, const T3& v3) const {  
                                                                 ^  
6 warnings generated.  
```

---
