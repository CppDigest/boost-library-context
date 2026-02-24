# #111 - msvc compile error C7631 [Open]

> Username: UMU618  
> Created at: 2023-02-24 12:29:37 UTC  
> Updated at: 2023-09-27 09:50:29 UTC  
> Url: https://github.com/boostorg/parameter/issues/111  

After I upgrading msvc to latest version, get error C7631.  
  
- Microsoft Visual Studio Community 2022 (64-bit) - Version 17.5.0  
  
- Boost 1.81.0  
  
```  
boost\boost\log\keywords\severity.hpp(32,1): error C7631: 'boost::log::v2_mt_nt6::keywords::`anonymous-namespace'::severity': variable with internal linkage declared but not defined  
```

---

## Comment 1

> Username: UMU618  
> Created at: 2023-02-24 13:35:00 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1443821466  

Boost is compiled with `b2.exe cxxstd=20 link=shared runtime-link=shared address-model=64 define=_WIN32_WINNT=0x0601`

---

## Comment 2

> Username: UMU618  
> Created at: 2023-02-24 14:48:14 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1443821476  

https://github.com/boostorg/parameter/blob/develop/include/boost/parameter/keyword.hpp#L716  
  
```cpp  
    namespace                                                                \  
    {                                                                        \  
        ::boost::parameter::keyword<tag_namespace::name> const& name         \  
            = ::boost::parameter::keyword<tag_namespace::name>::instance;    \  
    }  
```  
Add `static` to this line can fix this issue.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-02-24 15:10:00 UTC  
> Updated at: 2023-02-24 15:11:50 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1443823197  

This seems to be a compiler bug because `severity` is the variable definition, given that it has initializer.  
  
Please report this to MSVC developers.

---

## Comment 4

> Username: UMU618  
> Created at: 2023-02-25 04:58:34 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1444994222  

Already report [here](https://developercommunity.visualstudio.com/t/fatal--error-C1001-when-build-C-20-pro/10291086)

---

## Comment 5

> Username: Lastique  
> Created at: 2023-02-27 16:35:13 UTC  
> Updated at: 2023-02-27 16:39:04 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1446656697  

I have tried to build the current develop with the command you provided (plus `toolset=msvc-14.3`) and couldn't reproduce the error.  
  
Could you provide a reproducer?

---

## Comment 6

> Username: UMU618  
> Created at: 2023-02-28 07:31:46 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1447707692  

Sorry, I can't. This problem only arises when the code is sufficiently complex. Our projects have lots of C++ 20 modules, *.ixx. I use Boost.Log in small project, it compiles.

---

## Comment 7

> Username: Patschkowski  
> Created at: 2023-09-26 06:59:19 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1734941651  

As the previous thread was closed by Microsoft due to not enough input being provided by the author, I opened a new one with a small reproducer:  
  
https://developercommunity.visualstudio.com/t/error-C7631:-when-instantiating-template/10476144

---

## Comment 8

> Username: Patschkowski  
> Created at: 2023-09-27 09:50:29 UTC  
> Url: https://github.com/boostorg/parameter/issues/111#issuecomment-1737074426  

> As the previous thread was closed by Microsoft due to not enough input being provided by the author, I opened a new one with a small reproducer:  
>   
> https://developercommunity.visualstudio.com/t/error-C7631:-when-instantiating-template/10476144  
  
Microsoft responded that this is not a compiler bug.
