# #122 - Error introduced by boost::core::launder [Closed]

> Username: alandefreitas  
> Created at: 2024-01-03 19:33:03 UTC  
> Updated at: 2024-01-04 07:31:55 UTC  
> Closed at: 2024-01-03 22:21:12 UTC  
> Url: https://github.com/boostorg/optional/issues/122  

The Boost.URL drone job for Windows (x86) broke in the last days from an [old run](https://drone.cpp.al/boostorg/url/2322/4/2) to [a more recent run](https://drone.cpp.al/boostorg/url/2325/11/2). These two jobs are testing the very same commit: no code changes in Boost.URL. Also, it only fails in [MSVC 14.1](https://drone.cpp.al/boostorg/url/2325/11/2). [MSVC 14.3](https://github.com/boostorg/url/actions/runs/7391502840/job/20108488049) works fine.  
  
<details>  
  
  <summary>Output </summary>  
  
  
```  
c:\drone\boost-root\libs\url\src\detail\pattern.cpp(700) : fatal error C1001: An internal error has occurred in the compiler.  
(compiler file 'd:\agent\_work\2\s\src\vctools\compiler\utc\src\p2\main.c', line 187)  
 To work around this problem, try simplifying or changing the program near the locations listed above.  
Please choose the Technical Support command on the Visual C++   
 Help menu, or open the Technical Support help file for more information  
  
c:\drone\boost-root\libs\url\src\detail\pattern.cpp(700) : fatal error C1001: An internal error has occurred in the compiler.  
(compiler file 'd:\agent\_work\2\s\src\vctools\compiler\utc\src\common\error.c', line 835)  
 To work around this problem, try simplifying or changing the program near the locations listed above.  
Please choose the Technical Support command on the Visual C++   
 Help menu, or open the Technical Support help file for more information  
  
    call "bin.v2\standalone\msvc\msvc-14.1\adrs-mdl-32\archt-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\url\src\detail\pattern.cpp" -c -Fo"bin.v2\libs\url\test\limits\limits.test\msvc-14.1\rls\adrs-mdl-32\cxstd-17-iso\thrd-mlt\detail\pattern.obj"     -TP /wd4675 /EHs /std:c++17 /GR /Zc:throwingNew /O2 /Ob2 /W4 /WX /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /we4265 -DBOOST_ALL_NO_LIB=1 -DBOOST_URL_DYN_LINK=1 -DBOOST_URL_MAX_SIZE=16 -DBOOST_URL_NO_LIB -DBOOST_URL_STATIC_LINK -DNDEBUG "-I." "-Ilibs\url\extra" "-Ilibs\url\test\limits"   
  
...failed compile-c-c++ bin.v2\libs\url\test\limits\limits.test\msvc-14.1\rls\adrs-mdl-32\cxstd-17-iso\thrd-mlt\detail\pattern.obj...  
```  
  
</details>  
  
The message is very unhelpful but Peter helped me track down the error. We have a `pattern.cpp` source file that includes Boost.Optional and the error goes away when we check out any version of Boost.Optional before e31cf6f2. In particular, the error goes away if we comment out:  
  
```cpp  
    T const& ref() const { return * /*boost::core::launder*/(ptr_ref()); }  
    T &      ref()       { return * /*boost::core::launder*/(ptr_ref()); }  
```  
  
Unfortunately, I can't identify _why_ this is causing an error. We could only identify _what_ is causing it.  
  
This is also related to #121.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-03 20:48:40 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1875952440  

That's unfortunate.   
The only thing I can do is to revert https://github.com/boostorg/optional/pull/119.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2024-01-03 20:57:11 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1875962051  

Yes. I wish we knew why this is causing an error. We just know what is causing it.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-03 21:21:55 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1875988878  

This should be fixed now by https://github.com/boostorg/optional/commit/8375df7693340c4182121b9df8cfe8199e65eb33.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-01-03 22:21:12 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876046674  

Windows x86 is working now: https://drone.cpp.al/boostorg/url/2329/11/2  
  
Thanks!

---

## Comment 5

> Username: pdimov  
> Created at: 2024-01-04 00:36:33 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876154643  

It's better to use `BOOST_WORKAROUND(BOOST_MSVC, < 1920)` here because msvc-14.2 and above have no problems with `launder` and unversioned ifdefs aren't a good practice.  
  
But it might be even better to apply this workaround in `core/launder.hpp` instead.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-01-04 00:42:24 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876158436  

I just wonder if this is a problem with `launder` or with a combination of factors.

---

## Comment 7

> Username: pdimov  
> Created at: 2024-01-04 00:46:53 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876161262  

It's a compiler bug in VS2017 (32 bit); an internal compiler error with `toolset=msvc-14.1 cxxstd=17 variant=release address-model=32`.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2024-01-04 00:57:53 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876168410  

I mean, have other problems `std::launder` been reported on this setup, other than the Optional use case? If not, it will be safer to fix it only in Optional.

---

## Comment 9

> Username: alandefreitas  
> Created at: 2024-01-04 01:07:23 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876174388  

I think we only caught this bug in Boost.URL, but I think Christian has been using launder all around to eliminate `-Wmaybe-uninitialized` errors. I think this bug is being propagated to other libraries. Still, the only reason we're not catching this bug in more libraries is that most people are not testing this rather unusual combination of factors.

---

## Comment 10

> Username: pdimov  
> Created at: 2024-01-04 01:07:34 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876174505  

No, but `std::launder` hasn't seen much use. I think it's fairly safe to say that using it under MSVC 14.1 is likely to run into errors with more complex expressions, and there's probably no upside because the compiler probably doesn't need it.

---

## Comment 11

> Username: pdimov  
> Created at: 2024-01-04 01:08:11 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876174878  

Right, not many are testing msvc-14.1 in 32 bit mode.

---

## Comment 12

> Username: pdimov  
> Created at: 2024-01-04 03:24:42 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876257275  

https://github.com/boostorg/core/commit/e4adc769aa7b952f2719cee0958222bc80e1ce81 should make the workaround here unnecessary.

---

## Comment 13

> Username: akrzemi1  
> Created at: 2024-01-04 07:31:54 UTC  
> Url: https://github.com/boostorg/optional/issues/122#issuecomment-1876605489  

Thanks. I have reverted the fix in Optional.
