# #691 Fix warning from scoped_default_precision [Merged]

> Username: pps83  
> Created at: 2025-05-27 22:02:50 UTC  
> Updated at: 2025-05-28 19:44:25 UTC  
> Merged at: 2025-05-28 12:23:27 UTC  
> Closed at: 2025-05-28 12:23:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/691  



---

## Comment 1

> Username: pps83  
> Created_at: 2025-05-27 22:05:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/691#issuecomment-2914270692  

I get these warnings:  
  
```  
[3/9] Building CXX object CMakeFiles/common.dir/src/SplitsInfo.cpp.obj  
In file included from D:/work-pps/src/SplitsInfo.cpp:1:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/cpp_int.hpp:19:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/number.hpp:12:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/generic_interconvert.hpp:12:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/default_ops.hpp:4067:  
D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/no_et_ops.hpp:458:79: warning: unused variable 'precision_guard' [-Wunused-variable]  
  458 |          detail::scoped_default_precision<multiprecision::number<B, et_off> > precision_guard(a, b);  
      |                                                                               ^~~~~~~~~~~~~~~  
D:/work-pps/src/SplitsInfo.cpp:18:39: note: in instantiation of function template specialization 'boost::multiprecision::operator*<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, unsigned long long>' requested here  
   18 |         uint128_t x = uint128_t(from) * to_m;  
      |                                       ^  
In file included from D:/work-pps/src/SplitsInfo.cpp:1:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/cpp_int.hpp:19:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/number.hpp:12:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/generic_interconvert.hpp:12:  
D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/default_ops.hpp:3975:1: warning: unused variable 'precision_guard' [-Wunused-variable]  
 3975 | BINARY_OP_FUNCTOR(gcd, number_kind_integer)  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/default_ops.hpp:3669:82: note: expanded from macro 'BINARY_OP_FUNCTOR'  
 3669 |       detail::scoped_default_precision<multiprecision::number<Backend, et_off> > precision_guard(arg, a);                                                                                                                                                  \  
      |                                                                                  ^~~~~~~~~~~~~~~  
D:/work-pps/src/SplitsInfo.cpp:20:23: note: in instantiation of function template specialization 'boost::multiprecision::gcd<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>>' requested here  
   20 |         uint128_t z = gcd(x, y);  
      |                       ^  
In file included from D:/work-pps/src/SplitsInfo.cpp:1:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/cpp_int.hpp:19:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/number.hpp:12:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/generic_interconvert.hpp:12:  
In file included from D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/default_ops.hpp:4067:  
D:/work-pps/../boost_1_88_0/boost/multiprecision/detail/no_et_ops.hpp:141:79: warning: unused variable 'precision_guard' [-Wunused-variable]  
  141 |          detail::scoped_default_precision<multiprecision::number<B, et_off> > precision_guard(a, b);  
      |                                                                               ^~~~~~~~~~~~~~~  
D:/work-pps/src/SplitsInfo.cpp:21:27: note: in instantiation of function template specialization 'boost::multiprecision::operator/<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>>' requested here  
   21 |         from = uint64_t(x / z);  
      |                           ^  
  
```  
  
this is sample code:  
```c++  
#include <boost/multiprecision/cpp_int.hpp>  
#include <utility>  
#include <string_view>  
#include <stdint.h>  
  
std::pair<uint64_t, uint64_t> from_string(const std::string_view& s);  
  
std::pair<int, int> parse_splits(std::string_view split_from, std::string_view split_to)  
{  
    using namespace boost::multiprecision;  
    auto [from, from_m] = from_string(split_from);  
    auto [to, to_m] = from_string(split_to);  
    if (from_m != 1 || to_m != 1)  
    {  
        uint128_t x = uint128_t(from) * to_m;  
        uint128_t y = uint128_t(to) * from_m;  
        uint128_t z = gcd(x, y);  
        from = uint64_t(x / z);  
        to = uint64_t(y / z);  
    }  
    return {int(from), int(to)};  
}  
```  
  
I compile it with `c++23 -Wall`

---

## Comment 2

> Username: pps83  
> Created_at: 2025-05-27 22:06:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/691#issuecomment-2914273449  

`precision_guard` is created in many places, it's too much mess to mark it unused for each var. Making struct [[maybe_unused]] seems to be working

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-05-27 22:35:19 UTC  
> Updated_at: 2025-05-28 12:23:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/691#issuecomment-2914340909  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`eef4acf`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/eef4acfbe100cdbbf1ff9dc04491d52cc14c0fe1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7c7add2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/7c7add2397cce2662b35588c7221ba7db92bea72?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 51 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/691/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #691     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          279     280      +1       
  Lines        28979   29074     +95       
=========================================  
+ Hits         27253   27349     +96       
+ Misses        1726    1725      -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/detail/precision.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fprecision.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvcHJlY2lzaW9uLmhwcA==) | `98.6% <ø> (ø)` | |  
  
... and [9 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/691/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eef4acf...7c7add2](https://app.codecov.io/gh/boostorg/multiprecision/pull/691?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-05-28 12:23:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/691#issuecomment-2916105884  

CI Failures are unrelated, will investigate separately.

---
