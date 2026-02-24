# #203 Add check for -Woverflow in GCC testing [Merged]

> Username: mborland  
> Created at: 2024-06-06 10:40:29 UTC  
> Updated at: 2024-06-06 11:49:58 UTC  
> Merged at: 2024-06-06 11:44:50 UTC  
> Closed at: 2024-06-06 11:44:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/203  

Boost.Mysql with GCC-5 failed with:  
  
```  
In file included from ./boost/charconv/from_chars.hpp:11:0,  
                 from libs/mysql/test/unit/pch.hpp:18:  
./boost/charconv/detail/from_chars_integer_impl.hpp: In instantiation of 'boost::charconv::from_chars_result boost::charconv::detail::from_chars_integer_impl(const char*, const char*, Integer&, int) [with Integer = char; Unsigned_Integer = unsigned char; boost::charconv::from_chars_result = boost::charconv::from_chars_result_t<char>]':  
./boost/charconv/detail/from_chars_integer_impl.hpp:271:70:   required from 'boost::charconv::from_chars_result boost::charconv::detail::from_chars(const char*, const char*, Integer&, int) [with Integer = char; boost::charconv::from_chars_result = boost::charconv::from_chars_result_t<char>]'  
./boost/charconv/from_chars.hpp:25:55:   required from here  
./boost/charconv/detail/from_chars_integer_impl.hpp:111:28: error: large integer implicitly truncated to unsigned type [-Werror=overflow]  
             overflow_value = BOOST_CHARCONV_INT128_MAX;  
                            ^  
./boost/charconv/detail/from_chars_integer_impl.hpp:112:23: error: large integer implicitly truncated to unsigned type [-Werror=overflow]  
             max_digit = BOOST_CHARCONV_INT128_MAX;  
                       ^  
./boost/charconv/detail/from_chars_integer_impl.hpp:137:28: error: large integer implicitly truncated to unsigned type [-Werror=overflow]  
             overflow_value = BOOST_CHARCONV_UINT128_MAX;  
                            ^  
./boost/charconv/detail/from_chars_integer_impl.hpp:138:23: error: large integer implicitly truncated to unsigned type [-Werror=overflow]  
             max_digit = BOOST_CHARCONV_UINT128_MAX;  
```  
  
Those lines already have a pragma for that warning: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/from_chars_integer_impl.hpp#L62

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-06 11:25:30 UTC  
> Updated_at: 2024-06-06 11:31:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/203#issuecomment-2152110838  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/203?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.01%. Comparing base [(`9a3aa49`)](https://app.codecov.io/gh/boostorg/charconv/commit/9a3aa49fada1b56ffd397576846970651e701a08?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`75f1993`)](https://app.codecov.io/gh/boostorg/charconv/commit/75f1993ec7f37a08f785f8ee62116e930e8c0673?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/203/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/203?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #203   +/-   ##  
========================================  
  Coverage    94.01%   94.01%             
========================================  
  Files           66       66             
  Lines         8439     8439             
========================================  
  Hits          7934     7934             
  Misses         505      505             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/203?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/203?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9a3aa49...75f1993](https://app.codecov.io/gh/boostorg/charconv/pull/203?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: anarthal  
> Created_at: 2024-06-06 11:32:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/203#issuecomment-2152137979  

Been investigating and the problem is related to the use of precompiled headers (sigh). Seems that gcc-5 fails to correctly process the warning suppression pragmas when using PCHs. Since you already fixed #202, I've just taken out the header from my PCH.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-06-06 11:44:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/203#issuecomment-2152176186  

> Been investigating and the problem is related to the use of precompiled headers (sigh). Seems that gcc-5 fails to correctly process the warning suppression pragmas when using PCHs. Since you already fixed #202, I've just taken out the header from my PCH.  
  
Glad you found your answer. I'll merge this in anyway because it never hurts to check more warnings.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-06-06 11:49:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/203#issuecomment-2152193346  

-Woverflow is on by default if I'm not mistaken. You don't even need -Wall. https://godbolt.org/z/Wxb1rhEfM

---
