# #5 - Code gives compiler error when using VC++ new preprocessor [Closed]

> Username: eldiener  
> Created at: 2021-03-05 02:43:15 UTC  
> Updated at: 2021-03-18 13:16:05 UTC  
> Closed at: 2021-03-17 14:24:41 UTC  
> Url: https://github.com/boostorg/describe/issues/5  

The code:  
  
`#include <boost/describe.hpp>  
  
struct XXX  
    {  
    int x;  
    };  
struct YYY : public XXX  
    {  
    long y;  
    };  
      
BOOST_DESCRIBE_STRUCT(YYY,(XXX),(y))  
      
int main()   
    {  
    return 0;  
    }`  
  
compiles without error for gcc-10.2 in C++14 mode, clang-11.0 in C++14 mode, in clang-win-11.0 in C++14 mode, in VC++14.2 in C++14 mode using the default preprocessor, and in VC++14.2 Preview in C++14 mode using the default preprocessor. Yet if I try to compile it with VC++14.2 or VC++14.2 Preview, using the new C++ standard preprocessor which is triggered with the `/Zc:preprocessor` switch, the code gives the error:  
  
`test_describe.cpp  
test_describe.cpp(12): error C2589: ';': illegal token on right side of '::'  
test_describe.cpp(12): error C2062: type 'unknown-type' unexpected`  
  
I would try to report this to Microsoft but they would have to get Describe, put it into a Boost directory structure, and then try to test it out that way, which might be onerous for them. Obviously gcc and clang have extremely standard C++ preprocessors, so why the new VC++ preprocessor gives an error while gcc and clang do not is the mystery.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-05 03:29:29 UTC  
> Url: https://github.com/boostorg/describe/issues/5#issuecomment-791125055  

There's a MSVC-specific path in the code because of the use of `##__VA_ARGS__` which MSVC doesn't support and doesn't need:  
 https://github.com/pdimov/describe/blob/eea288e04961374721020b5454707590815b2fcf/include/boost/describe/detail/members.hpp#L50  
  
I suppose that the new preprocessor needs to take the conforming path.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-05 03:39:19 UTC  
> Url: https://github.com/boostorg/describe/issues/5#issuecomment-791128608  

Or, since `##__VA_ARGS__` is a GCC extension, perhaps the new MSVC preprocessor needs `__VA_OPT__(,)` instead. I'll figure it out when I have time. :-)

---

## Comment 3

> Username: eldiener  
> Created at: 2021-03-05 06:13:42 UTC  
> Updated at: 2021-03-05 06:14:21 UTC  
> Url: https://github.com/boostorg/describe/issues/5#issuecomment-791183622  

The old, default preprocessor is:  
  
`if !defined(_MSVC_TRADITIONAL) || _MSVC_TRADITIONAL`  
  
as opposed to the new preprocessor. I would not have any workarounds for the new preprocessor until there is some proof it needs something.  
  
Identifying VC++ should be something like:  
  
`if defined(_MSC_VER) && !defined(__clang__)`  
  
You can look at the logic in the Preprocessor's config.hpp, where `BOOST_PP_CONFIG_MSVC()` identifies the old, default preprocessor and the new preprocessor is considered a strictly conforming C++ standard preprocessor, `BOOST_PP_CONFIG_STRICT()`. My testing has shown that the new preprocessor is extremely good, but of course I would not rule out any possible bugs which may still be there. But with the latest releases in both VS2019 or the VS2019 preview I have not found any bugs anymore.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-03-05 19:34:34 UTC  
> Url: https://github.com/boostorg/describe/issues/5#issuecomment-791635204  

As I said `##__VA_ARGS__` is a non-conforming GCC extension. However, https://docs.microsoft.com/en-us/cpp/preprocessor/preprocessor-experimental-overview?view=msvc-160 says that the new MSVC preprocessor does support it, so using it should be fine. I'll change the condition to check `_MSVC_TRADITIONAL` after the review ends.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-03-17 14:24:41 UTC  
> Url: https://github.com/boostorg/describe/issues/5#issuecomment-801123300  

This should be fixed now.

---

## Comment 6

> Username: eldiener  
> Created at: 2021-03-18 13:16:05 UTC  
> Url: https://github.com/boostorg/describe/issues/5#issuecomment-801917789  

It is. Bravo !
