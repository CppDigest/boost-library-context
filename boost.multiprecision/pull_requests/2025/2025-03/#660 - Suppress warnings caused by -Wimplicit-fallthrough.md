# #660 Suppress warnings caused by -Wimplicit-fallthrough [Merged]

> Username: koct9i  
> Created at: 2025-03-10 10:41:16 UTC  
> Updated at: 2025-03-25 18:09:08 UTC  
> Merged at: 2025-03-10 15:31:23 UTC  
> Closed at: 2025-03-10 15:31:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660  

Example of the warning:  
  
```  
restricted/boost/multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:1920:4: error: unannotated fall-through between switch labels [-Werror,-Wimplicit-fallthrough]  
 1920 |    case cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>::exponent_zero:  
      |    ^  
restricted/boost/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3881:1: note: in instantiation of function template specialization 'boost::multiprecision::backends::eval_floor<64U, boost::multiprecision::backends::digit_base_2, void, short, (short)-16382, (short)16383>' requested here  
 3881 | UNARY_OP_FUNCTOR(floor, number_kind_floating_point)  
      | ^  
restricted/boost/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3505:7: note: expanded from macro 'UNARY_OP_FUNCTOR'  
 3505 |       BOOST_JOIN(eval_, func)(result.backend(), arg.backend());                                                                                                                                                                  \  
      |       ^  
restricted/boost/config/include/boost/config/helper_macros.hpp:33:26: note: expanded from macro 'BOOST_JOIN'  
   33 | #define BOOST_JOIN(X, Y) BOOST_DO_JOIN(X, Y)  
      |                          ^  
restricted/boost/config/include/boost/config/helper_macros.hpp:34:29: note: expanded from macro 'BOOST_DO_JOIN'  
   34 | #define BOOST_DO_JOIN(X, Y) BOOST_DO_JOIN2(X,Y)  
      |                             ^  
restricted/boost/config/include/boost/config/helper_macros.hpp:35:30: note: expanded from macro 'BOOST_DO_JOIN2'  
   35 | #define BOOST_DO_JOIN2(X, Y) X##Y  
      |                              ^  
<scratch space>:104:1: note: expanded from here  
  104 | eval_floor  
      | ^  
clickhouse/base/base/wide_integer_impl.h:1063:16: note: in instantiation of member function 'wide::integer<128, unsigned int>::_impl::wide_integer_from_builtin' requested here  
 1063 |         _impl::wide_integer_from_builtin(*this, rhs);  
      |                ^  
clickhouse/src/Formats/JSONExtractTree.h:162:59: note: in instantiation of function template specialization 'accurate::convertNumeric<double, wide::integer<128, unsigned int>, false>' requested here  
  162 |             else if (!allow_type_conversion || !accurate::convertNumeric<Float64, NumberType, false>(element.getDouble(), value))  
      |                                                           ^  
clickhouse/src/Formats/JSONExtractTree.h:287:14: note: in instantiation of function template specialization 'DB::tryGetNumericValueFromJSONElement<NYT::NClickHouseServer::TYsonParserAdapter, wide::integer<128, unsigned int>>' requested here  
  287 |         if (!tryGetNumericValueFromJSONElement<JSONParser, NumberType>(value, element, insert_settings.convert_bool_to_integer || is_bool_type, insert_settings.allow_type_conversion, error))  
      |              ^  
clickhouse/src/Formats/JSONExtractTree.h:254:7: note: in instantiation of member function 'DB::NumericNode<NYT::NClickHouseServer::TYsonParserAdapter, wide::integer<128, unsigned int>>::insertResultToColumn' requested here  
  254 | class NumericNode : public JSONExtractTreeNode<JSONParser>  
      |       ^  
/home/khlebnikov/src/ytsaurus/yt/yt/library/clickhouse_functions/ypath.cpp:570:28: note: in instantiation of function template specialization 'DB::buildJSONExtractTree<NYT::NClickHouseServer::TYsonParserAdapter>' requested here  
  570 |         auto extractTree = buildJSONExtractTree<TYsonParserAdapter>(returnType, nullptr /*source_for_exception_message*/);  
      |                            ^  
/home/khlebnikov/src/ytsaurus/yt/yt/library/clickhouse_functions/ypath.cpp:492:7: note: in instantiation of member function 'NYT::NClickHouseServer::TFunctionYPathExtractImpl<true, NYT::NClickHouseServer::TNameYPathExtractStrict>::executeImpl' requested here  
  492 | class TFunctionYPathExtractImpl : public IFunction  
      |       ^  
/home/khlebnikov/src/ytsaurus/yt/yt/library/clickhouse_functions/ypath.cpp:665:13: note: in instantiation of function template specialization 'DB::FunctionFactory::registerFunction<NYT::NClickHouseServer::TFunctionYPathExtractImpl<true, NYT::NClickHouseServer::TNameYPathExtractStrict>>' requested here  
  665 |     factory.registerFunction<TFunctionYPathExtractStrict>();  
      |             ^  
restricted/boost/multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:1920:4: note: insert 'BOOST_FALLTHROUGH;' to silence this warning  
 1920 |    case cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>::exponent_zero:  
      |    ^  
      |    BOOST_FALLTHROUGH;   
restricted/boost/multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:1920:4: note: insert 'break;' to avoid fall-through  
 1920 |    case cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>::exponent_zero:  
      |    ^  
      |    break;   
1 error generated.  
Failed  
  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2025-03-10 13:47:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2710655428  

@koct9i I added a commit to define our own version of the fall through macro to keep support for standalone use of the library. Let me know if that still works for your issue. I also kicked off the CI run.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-03-10 14:28:40 UTC  
> Updated_at: 2025-03-10 15:40:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2710793914  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`ce3229e`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/ce3229eb23c58f141019302f0874d038fdd916b2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`bdc3339`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/bdc3339dbfb6707b80ea9b6897f1717990e1c7cf?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/660/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #660     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          279     279               
  Lines        28979   28979               
=========================================  
- Hits         27255   27253      -2       
- Misses        1724    1726      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `89.7% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/660/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ce3229e...bdc3339](https://app.codecov.io/gh/boostorg/multiprecision/pull/660?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary>🚀 New features to boost your workflow: </summary>  
  
- ❄ [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: koct9i  
> Created_at: 2025-03-10 15:28:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2710984368  

Looks good to me.

---

## Comment 4

> Username: mborland  
> Created_at: 2025-03-10 15:31:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2710993034  

> Looks good to me.  
  
Perfect. I'll ask to merge this as bug fix for 1.88 which is due for release on 9 April.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-03-25 14:12:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2751403640  

Um I hate to find this, but I fear there might be a problem with the new work on fall-thorugh. I activated one of the CI/CD runs in an unrelated project yesterday and goth some [warnings on C++14](https://github.com/ckormanyos/wide-decimal/actions/runs/14050081366/job/39338696121).  
  
It seems like tedious typing, but the fall-through macro miight be causing trouble on C++14 when the `[[fallthrough]]` attribute is not yet supported. Or am I running into something different and new?  
  
Cc: @mborland and @koct9i

---

## Comment 6

> Username: mborland  
> Created_at: 2025-03-25 14:17:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2751418854  

I can further wrap the definition. I'm not sure why that version of clang is saying it has the attribute when it shouldn't, but it'll happen to others I'm sure.

---

## Comment 7

> Username: mborland  
> Created_at: 2025-03-25 18:09:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/660#issuecomment-2752129247  

See #668

---
