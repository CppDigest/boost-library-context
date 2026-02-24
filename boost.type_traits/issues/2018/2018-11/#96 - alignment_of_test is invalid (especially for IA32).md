# #96 - alignment_of_test is invalid (especially for IA32) [Open]

> Username: kuhlenough  
> Created at: 2018-11-09 18:29:37 UTC  
> Updated at: 2018-11-09 18:29:37 UTC  
> Url: https://github.com/boostorg/type_traits/issues/96  

This test determines that the __alignof() operator returns the same value as an actual compiler alignment. However the __alignof (type) is suppose to return the minimal required alignment for the type, not the actual alignment the compiler uses. So on IA32 where an alignment of 1 works, and the ABI specifies a minimum of 4;  __alignof(type) returns 4, which is correct.  However in almost all cases the compiler will align at 8, for performance reasons and the test fails.  On all other configs we tested the actual, and minimal alignment for clang 6.0 are the same.  
Leading to misinformed comment here https://github.com/boostorg/type_traits/include/boost/type_traits/intrinsics.hpp#L289  
and a fall-back of not using actual __alignof() operator for most of Boost's supported platforms.
