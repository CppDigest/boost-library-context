# #16 - Many tests failed in compilation for "comparison of integers of different signs" in Windows [Closed]

> Username: yuxianch  
> Created at: 2019-11-18 03:07:16 UTC  
> Updated at: 2019-11-18 05:32:26 UTC  
> Closed at: 2019-11-18 05:32:26 UTC  
> Url: https://github.com/boostorg/variant2/issues/16  

Hi,  
I found that many tests failed in compilation for "comparison of integers of different signs" in windows. Take variant_copy_assign.cpp as an example. When executing below command,   
`clang-cl -m32 variant_copy_assign.cpp -o variant_copy_assign.o /TP /Z7 /Od  /Ob0 /W4 /WX /GR /MDd /EHs /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -I"..\..\.."`  
it will get following error message, which is caused by the return value type `size_t` of function `index()`.  
  
> In file included from variant_copy_assign.cpp:10:  
..\..\..\boost/core/lightweight_test.hpp(142,62): error: comparison of integers of different signs: 'const unsigned int' and 'const int' [-Werror,-Wsign-compare]  
    bool operator()(const T& t, const U& u) const { return t == u; }  
                                                           ~ ^  ~  
..\..\..\boost/core/lightweight_test.hpp(181,9): note: in instantiation of function template specialization 'boost::detail::lw_test_eq::operator()<unsigned int, int>' requested here  
    if( pred(t, u) )  
        ^  
variant_copy_assign.cpp(84,9): note: in instantiation of function template specialization 'boost::detail::test_with_impl<boost::detail::lw_test_eq, unsigned int, int>' requested here  
        BOOST_TEST_EQ( v.index(), 0 );  
        ^  
..\..\..\boost/core/lightweight_test.hpp(405,55): note: expanded from macro 'BOOST_TEST_EQ'  
#define BOOST_TEST_EQ(expr1,expr2) ( ::boost::detail::test_with_impl(::boost::detail::lw_test_eq(), #expr1, #expr2, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION, expr1, expr2) )  
                                                      ^  
1 error generated.  
  
This test failed in 64-bit mode too. For 32-bit mode clang-cl, size_t means `unsigned int`, while for 64-bit mode clang-cl, it means `unsigned long long`. These two types are both different with expected type `int`, so the test got failed.  
Can we make some changes to avoid this issue? Maybe change `size_t` to `int` for the return value type of `index()`?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-11-18 03:45:04 UTC  
> Url: https://github.com/boostorg/variant2/issues/16#issuecomment-554838905  

This should be fixed by https://github.com/boostorg/core/commit/e38997be4dc75d94272f74e733a065d089ab291e.

---

## Comment 2

> Username: yuxianch  
> Created at: 2019-11-18 05:32:25 UTC  
> Url: https://github.com/boostorg/variant2/issues/16#issuecomment-554858900  

@pdimov Yes, you are right. They ignored this kind of errors. Thank you so much.  
I will close this issue.
