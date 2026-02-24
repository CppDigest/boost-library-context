# #44 - C++20 not supported [Closed]

> Username: heretic13  
> Created at: 2021-10-19 14:59:06 UTC  
> Updated at: 2021-11-03 17:19:17 UTC  
> Closed at: 2021-11-03 17:19:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/44  

Hello.  
  
VS2019 v. 16.11.2 , VS2022 17.0.0 RC   
OS Windows 10 x64  
boost 1.76  
  
C++20 enabled in settings  
----  
in file <boost/coroutine2/detail/wrap.hpp>, in class wrapper  
`  
boost::context::fiber  
    operator()( boost::context::fiber && c) {  
        return boost::context::detail::invoke(  
                std::move( fn1_),  
                fn2_,  
                std::forward< boost::context::fiber >( c) );  
    }  
`  
  
boost::context::detail::invoke uses std::result_of.  
std::result_of was removed from the C++17 standard.  
Test program won't compile

---

## Comment 1

> Username: heretic13  
> Created at: 2021-10-19 14:59:47 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/44#issuecomment-946812769  

Details. And the test program is here  
https://github.com/boostorg/context/issues/187

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-03 15:59:00 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/44#issuecomment-959550687  

I can confirm that the tests of Coroutine2 fail under msvc-14.2 with cxxstd=latest.  
```  
compile-c-c++ ..\..\bin.v2\libs\coroutine2\test\test_coroutine_asm.test\msvc-14.2\release\cxxstd-latest-iso\link-static\threading-multi\test_coroutine.obj  
test_coroutine.cpp  
..\..\boost/context/detail/invoke.hpp(29): error C2039: 'result_of': is not a member of 'std'  
..\..\boost/smart_ptr/intrusive_ptr.hpp(392): note: see declaration of 'std'  
```  
...  
```  
compile-c-c++ ..\..\bin.v2\libs\coroutine2\test\test_coroutine_native.test\msvc-14.2\release\context-impl-winfib\cxxstd-latest-iso\link-static\threading-multi\test_coroutine.obj  
test_coroutine.cpp  
..\..\boost/context/detail/invoke.hpp(29): error C2039: 'result_of': is not a member of 'std'  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\map(25): note: see declaration of 'std'  
```

---

## Comment 3

> Username: olk  
> Created at: 2021-11-03 16:46:36 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/44#issuecomment-959711751  

@pdimov I've pushed commit d7e1c1c - could you verify that the problem is fixed, please?

---

## Comment 4

> Username: pdimov  
> Created at: 2021-11-03 16:49:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/44#issuecomment-959720795  

Tests pass now under msvc-14.2 and msvc-14.3 with cxxstd=latest.

---

## Comment 5

> Username: olk  
> Created at: 2021-11-03 17:19:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/44#issuecomment-959750678  

> Tests pass now under msvc-14.2 and msvc-14.3 with cxxstd=latest.  
  
ty
