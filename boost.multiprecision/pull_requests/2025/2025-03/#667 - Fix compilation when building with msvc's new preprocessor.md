# #667 Fix compilation when building with msvc's new preprocessor [Merged]

> Username: pps83  
> Created at: 2025-03-24 13:48:59 UTC  
> Updated at: 2025-03-25 18:48:29 UTC  
> Merged at: 2025-03-25 18:25:42 UTC  
> Closed at: 2025-03-25 18:25:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667  

fixes the following error when standard conforming preprocessor is enabled (`/Zc:preprocessor` option)  
```  
1>time.cpp  
1>D:\work-pps\boost_1_87_0\boost\multiprecision\cpp_int\literals.hpp(270,1): warning C5103: pasting '""_cppi' and '128' does not result in a valid preprocessing token  
1>(compiling source file '../src/time.cpp')  
1>    D:\work-pps\boost_1_87_0\boost\multiprecision\cpp_int\literals.hpp(252,9):  
1>    in expansion of macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
1>    D:\work-pps\boost_1_87_0\boost\config\helper_macros.hpp(33,9):  
1>    in expansion of macro 'BOOST_JOIN'  
1>    D:\work-pps\boost_1_87_0\boost\config\helper_macros.hpp(34,9):  
1>    in expansion of macro 'BOOST_DO_JOIN'  
1>    D:\work-pps\boost_1_87_0\boost\config\helper_macros.hpp(35,9):  
1>    in expansion of macro 'BOOST_DO_JOIN2'  
1>D:\work-pps\boost_1_87_0\boost\multiprecision\cpp_int\literals.hpp(270,1): error C2988: unrecognizable template declaration/definition  
```

---

## Comment 1

> Username: pps83  
> Created_at: 2025-03-24 13:51:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2748206760  

Perhaps, it's better to   
```c++  
#define BOOST_MP_LIT(P, N) BOOST_JOIN(operator"", BOOST_JOIN(P, N))  
```  
This way grepping for `operator""` would work. Lmk if I should update the PR

---

## Comment 2

> Username: mborland  
> Created_at: 2025-03-24 13:55:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2748218169  

> Perhaps, it's better to  
>   
> ```c++  
> #define BOOST_MP_LIT(P, N) BOOST_JOIN(operator"", BOOST_JOIN(P, N))  
> ```  
>   
> This way grepping for `operator""` would work. Lmk if I should update the PR  
  
I think this would be better

---

## Comment 3

> Username: pps83  
> Created_at: 2025-03-24 13:57:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2748225169  

> ...  
> This way grepping for `operator""` would work. Lmk if I should update the PR  
  
applied. This way it's better

---

## Comment 4

> Username: mborland  
> Created_at: 2025-03-24 13:58:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2748226814  

> > ...  
> > This way grepping for `operator""` would work. Lmk if I should update the PR  
>   
> applied. This way it's better  
  
Thanks. I approved the CI run

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2025-03-24 14:33:30 UTC  
> Updated_at: 2025-03-25 18:26:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2748339583  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`7fac807`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/7fac807c4624fb27e8b2f75433028d926b8ba3f4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d927981`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d9279817061b58b9201ef74980307fcb9a2089f6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/667/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #667   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          279     279             
  Lines        28979   28979             
=======================================  
  Hits         27253   27253             
  Misses        1726    1726             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/literals.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fliterals.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2xpdGVyYWxzLmhwcA==) | `100.0% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7fac807...d927981](https://app.codecov.io/gh/boostorg/multiprecision/pull/667?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2025-03-24 19:20:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2749175328  

How does this interact with https://github.com/boostorg/multiprecision/pull/665 and https://github.com/boostorg/multiprecision/pull/663 ?

---

## Comment 7

> Username: mborland  
> Created_at: 2025-03-24 19:29:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2749193404  

> How does this interact with #665 and #663 ?  
  
Good catch. I just checked out this commit and it does not cause "-Wdeprecated-literal-operator" to be emitted. I believe it's safe since that was the only intention of those two PRs.

---

## Comment 8

> Username: pps83  
> Created_at: 2025-03-24 19:51:17 UTC  
> Updated_at: 2025-03-24 20:22:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2749237348  

> How does this interact with #665 and #663 ?  
  
I also build with clang and had these warnings. After I took latest updates that fixed them I had issues with msvc. With this PR both issues should be resolved now.

---

## Comment 9

> Username: pps83  
> Created_at: 2025-03-25 18:26:54 UTC  
> Updated_at: 2025-03-25 18:29:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2752173987  

I was about to ping... there is a less intrusive option also:  
**from:**  
```c++  
operator BOOST_JOIN(""_cppi, Bits)  
```  
**to:**  
```c++  
BOOST_JOIN(operator ""_cppi, Bits)  
```  
  
even this one, as there were no reason to use BOOST_JOIN there:  
```c++  
operator ""_cppi##Bits   
```

---

## Comment 10

> Username: pps83  
> Created_at: 2025-03-25 18:27:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2752175454  

let me know if I should create a new pr and remove `BOOST_MP_LIT` thing completely

---

## Comment 11

> Username: mborland  
> Created_at: 2025-03-25 18:32:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/667#issuecomment-2752186762  

I think it's fine. We can still grep for operator "" in the changes from this PR which we weren't able to do previously.

---
