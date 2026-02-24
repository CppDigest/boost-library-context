# #187 - C++20 not supported [Closed]

> Username: heretic13  
> Created at: 2021-09-14 15:49:02 UTC  
> Updated at: 2021-11-03 18:45:39 UTC  
> Closed at: 2021-11-03 18:45:39 UTC  
> Url: https://github.com/boostorg/context/issues/187  

Hello.  
  
VS2019 v. 16.11.2  
OS Windows 10 x64  
boost 1.76  
  
C++20 enabled in settings  
[TestCoro.cpp.txt](https://github.com/boostorg/context/files/7163305/TestCoro.cpp.txt)  
![Error](https://user-images.githubusercontent.com/11976009/133290590-e9c404c4-5e93-4dec-bca1-868ef0819c36.png)  
  
sample program cannot compile  
  
I think this is due to the fact that std::result_of was removed from the C++17 standard.

---

## Comment 1

> Username: olk  
> Created at: 2021-10-02 18:37:33 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-932800695  

std::result_of is wrapped by BOOST_NO_CXX17_STD_INVOKE  
if BOOST_NO_CXX17_STD_INVOKE is defined std::result_of is used otherwise std::invoke_result/std::invoke gets compiled in

---

## Comment 2

> Username: olk  
> Created at: 2021-10-02 18:46:37 UTC  
> Updated at: 2021-10-02 18:47:06 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-932802123  

compiles and runs unit tests successfully with gcc-11.1.0:  
`b2 cxxflags="-std=c++11"`  
`b2 cxxflags="-std=c++17"`  
`b2 cxxflags="-std=c++20"`

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-10-03 00:57:10 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-932842206  

> std::result_of is wrapped by BOOST_NO_CXX17_STD_INVOKE if BOOST_NO_CXX17_STD_INVOKE is defined std::result_of is used otherwise std::invoke_result/std::invoke gets compiled in  
  
It actually does not: https://github.com/boostorg/context/blob/8289091f52da65422b6795d58295dea5fd9297c6/include/boost/context/detail/invoke.hpp#L7-L50  
  
> compiles and runs unit tests successfully with gcc-11.1.0: `b2 cxxflags="-std=c++11"` `b2 cxxflags="-std=c++17"` `b2 cxxflags="-std=c++20"`  
  
The report is about MSVC:   
  
> Hello.  
>   
> VS2019 v. 16.11.2 OS Windows 10 x64 boost 1.76  
>   
> ...  
  
See https://godbolt.org/z/Kq8W6dvzz

---

## Comment 4

> Username: olk  
> Created at: 2021-10-03 08:02:12 UTC  
> Updated at: 2021-10-03 08:05:20 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-932884571  

> > std::result_of is wrapped by BOOST_NO_CXX17_STD_INVOKE if BOOST_NO_CXX17_STD_INVOKE is defined std::result_of is used otherwise std::invoke_result/std::invoke gets compiled in  
>   
> It actually does not:  
>   
> https://github.com/boostorg/context/blob/8289091f52da65422b6795d58295dea5fd9297c6/include/boost/context/detail/invoke.hpp#L7-L50  
  
`std::result_of()` is used by `boost::context::detail::invoke()`. This function is wrapped, e.g. it gets called when `std::invoke()` is not available:  
```  
#if defined(BOOST_NO_CXX17_STD_INVOKE)  
#include <boost/context/detail/invoke.hpp>  
#endif  
...  
#if defined(BOOST_NO_CXX17_STD_INVOKE)  
        Ctx c = boost::context::detail::invoke( fn_, Ctx{ fctx } );  
#else  
        Ctx c = std::invoke( fn_, Ctx{ fctx } );  
#endif  
```  
  
` std::result_of()`  is defined in C++11 and C++17 - removed by C++20.  
But `std::invoke()` is defined since C++17.  
Therefore `boost::context::detail::invoke()` is only used/compiled (with `std::result_of()`) when `std::invoke()` is not defined.  
If you compile with MSVC and C++20 `std::invoke()` should be available and the code using `std::result_of()` gets never compiled.  
  
> The report is about MSVC:  
  
Seams there is something wrong with your MSVC (installation)?!

---

## Comment 5

> Username: pdimov  
> Created at: 2021-11-03 15:49:10 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-959517442  

I can confirm that the test fails under msvc-14.2 with cxxstd=latest.  
```  
...found 2940 targets...  
...updating 4 targets...  
compile-c-c++ ..\..\bin.v2\libs\context\test\test_invoke.test\msvc-14.2\release\cxxstd-latest-iso\link-static\threading-multi\test_invoke.obj  
test_invoke.cpp  
..\..\boost/context/detail/invoke.hpp(29): error C2039: 'result_of': is not a member of 'std'  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\map(25): note: see declaration of 'std'  
```  
It works with cxxstd=17.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-11-03 15:53:39 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-959532892  

Although looking at the test, it uses detail/invoke.hpp directly, so it's probably supposed to fail.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-11-03 15:57:27 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-959545767  

Looks like the problem is actually in Coroutine2, which uses detail::invoke without checking it.  
  
This would have worked before https://github.com/boostorg/context/commit/5df4efe14619cf3cf48c0faeeeaf426e4029d031.

---

## Comment 8

> Username: olk  
> Created at: 2021-11-03 17:27:30 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-959758343  

> Although looking at the test, it uses detail/invoke.hpp directly, so it's probably supposed to fail.  
  
@pdimov should be fixed with commit 885130b - could your verify for msvc-14, please?

---

## Comment 9

> Username: pdimov  
> Created at: 2021-11-03 17:33:07 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-959763311  

Works.

---

## Comment 10

> Username: olk  
> Created at: 2021-11-03 18:45:39 UTC  
> Url: https://github.com/boostorg/context/issues/187#issuecomment-959820723  

ty
