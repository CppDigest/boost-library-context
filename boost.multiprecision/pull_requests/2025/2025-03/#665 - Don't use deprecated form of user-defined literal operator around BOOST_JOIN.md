# #665 Don't use deprecated form of user-defined literal operator around BOOST_JOIN [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2025-03-17 22:49:14 UTC  
> Updated at: 2025-03-18 09:51:56 UTC  
> Merged at: 2025-03-18 09:51:30 UTC  
> Closed at: 2025-03-18 09:51:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/665  

Hi,  
  
I am working in @Thomas-Barbier-1A team, there is an additional fix needed to fully fix the issue #663 and merged into master with #664, the one around the `BOOST_JOIN` construction.  
  
To reproduce, you just need a clang 20 compiler, which doesn't consider boost as "system header" (as otherwise all warnings are silently ignored), and create dummy `plop.cpp` file containing just that:  
  
```  
#include "boost/multiprecision/cpp_int.hpp"  
```  
  
Then a simple compilation like this:  
  
```  
rgeissler@ncerndobedev6097:~/wk/tmp> /opt/1A/toolchain/x86_64-v23/bin/clang++ -I /data/mwrep/res/osp/Boost/23-0-0-5/include -o plop.o -c plop.cpp  
```  
  
Will give some errors like this:  
  
```  
/data/mwrep/res/osp/Boost/23-0-0-5/include/boost/multiprecision/cpp_int/literals.hpp:270:1: warning: identifier '_cppi128' preceded by whitespace in a literal operator declaration is deprecated [-Wdeprecated-literal-operator]   
  270 | BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL(128)  
      | ^  
/data/mwrep/res/osp/Boost/23-0-0-5/include/boost/multiprecision/cpp_int/literals.hpp:254:199: note: expanded from macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
  254 |    constexpr boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<Bits, Bits, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> > operator"" BOOST_JOIN(_cppi, Bits)()    \   
      |                                                                                                                                                                                                       ^  
/data/mwrep/res/osp/Boost/23-0-0-5/include/boost/config/helper_macros.hpp:33:26: note: expanded from macro 'BOOST_JOIN'  
   33 | #define BOOST_JOIN(X, Y) BOOST_DO_JOIN(X, Y)  
      |                          ^  
/data/mwrep/res/osp/Boost/23-0-0-5/include/boost/config/helper_macros.hpp:34:29: note: expanded from macro 'BOOST_DO_JOIN'  
   34 | #define BOOST_DO_JOIN(X, Y) BOOST_DO_JOIN2(X,Y)  
      |                             ^  
/data/mqwrep/res/osp/Boost/23-0-0-5/include/boost/config/helper_macros.hpp:35:30: note: expanded from macro 'BOOST_DO_JOIN2'  
   35 | #define BOOST_DO_JOIN2(X, Y) X##Y  
      |                              ^  
<scratch space>:98:1: note: expanded from here  
   98 | _cppi128  
      | ^  
In file included from plop.cpp:1:  
In file included from /data/mwrep/res/osp/Boost/23-0-0-5/include/boost/multiprecision/cpp_int.hpp:2344:  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-03-17 23:23:23 UTC  
> Updated_at: 2025-03-18 09:51:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/665#issuecomment-2731175611  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`c42d8a9`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/c42d8a94f12a1e2af3c84b67b6efbda36430a6db?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2ed2ef7`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/2ed2ef7e4048b18592efc73131170b902e2806b2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/665/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #665     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          279     279               
  Lines        28979   28979               
=========================================  
+ Hits         27253   27255      +2       
+ Misses        1726    1724      -2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/literals.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fliterals.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2xpdGVyYWxzLmhwcA==) | `100.0% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/665/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c42d8a9...2ed2ef7](https://app.codecov.io/gh/boostorg/multiprecision/pull/665?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary>🚀 New features to boost your workflow: </summary>  
  
- ❄ [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
