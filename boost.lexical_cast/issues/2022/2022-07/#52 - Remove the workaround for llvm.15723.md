# #52 - Remove the workaround for llvm.15723? [Closed]

> Username: Chandlerooo  
> Created at: 2022-07-13 13:40:35 UTC  
> Updated at: 2022-09-01 18:17:18 UTC  
> Closed at: 2022-09-01 18:17:17 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/52  

I find a workaround for llvm.15723 in lexical_cast_test.cpp:https://github.com/boostorg/lexical_cast/blob/a9f296115967335aaa9f4a4cadfd900f131a60d1/test/lexical_cast_test.cpp  
```cpp  
#ifndef _LIBCPP_VERSION  
    // libc++ had a bug in implementation of stream conversions for values that must be represented as infinity.  
    // http://llvm.org/bugs/show_bug.cgi?id=15723#c4  
    BOOST_CHECK_THROW(lexical_cast<test_t>(s_max_value+"1"), bad_lexical_cast);  
    BOOST_CHECK_THROW(lexical_cast<test_t>(s_max_value+"9"), bad_lexical_cast);  
  
    // VC9 can fail the following tests on floats and doubles when using stingstream...  
    BOOST_CHECK_THROW(lexical_cast<test_t>("1"+s_max_value), bad_lexical_cast);  
    BOOST_CHECK_THROW(lexical_cast<test_t>("9"+s_max_value), bad_lexical_cast);  
#endif  
```  
This compiler bug is marked as fixed: http://llvm.org/bugs/show_bug.cgi?id=15723  
Shall the workaround be removed?

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-08-31 18:37:20 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/52#issuecomment-1233283508  

It could be disabled for new versions of libc++. PR would be welcomed:)

---

## Comment 2

> Username: apolukhin  
> Created at: 2022-09-01 18:17:17 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/52#issuecomment-1234626593  

Feel free to create a PR if you wish. Closing this issue
