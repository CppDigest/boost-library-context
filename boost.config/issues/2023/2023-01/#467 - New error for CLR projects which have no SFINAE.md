# #467 - New error for CLR projects which have no SFINAE [Closed]

> Username: greenkalx  
> Created at: 2023-01-12 19:49:15 UTC  
> Updated at: 2023-01-14 09:09:35 UTC  
> Closed at: 2023-01-14 09:09:35 UTC  
> Url: https://github.com/boostorg/config/issues/467  

For Windows managed C++ CLR projects, the _M_CEE flag is set, which then sets BOOST_NO_SFINAE_EXPR in visualc.hpp.  
This results in a new compile error in cxx03_warn.hpp:  
`1>c:\work\external\libs\Boost\boost_1_81_0\boost\math\tools\cxx03_warn.hpp(92,1): fatal  error C1189: #error:  Support for C++03 has been removed. The minimum requirement for this library is fully compliant C++11.  
`  
  
So CLR projects are no longer supported?  
Can I set a flag to exclude features that use SFINAE?  
  
My temporary workaround is to comment out the BOOST_NO_SFINAE_EXPR check in cxx03_warn.hpp:  
```  
//#if defined(BOOST_NO_SFINAE_EXPR) && !defined(BOOST_MATH_SHOW_CXX03_WARNING)  
//#  define BOOST_MATH_SHOW_CXX03_WARNING  
//#  define BOOST_MATH_CXX03_WARN_REASON "BOOST_NO_SFINAE_EXPR"  
//#endif  
```  
  
System is Windows 10, compiling with Visual Studio 2022 C++17.  
With Boost v1.81.0 (upgrading from v1.65.1)

---

## Comment 1

> Username: greenkalx  
> Created at: 2023-01-12 19:49:23 UTC  
> Url: https://github.com/boostorg/config/issues/467#issuecomment-1380924576  

BOOST_NO_SFINAE_EXPR seems the only flag that triggers the error in cxx03_warn.hpp, none of the other BOOST_NO_CXX11_* flags trigger it.

---

## Comment 2

> Username: greenkalx  
> Created at: 2023-01-13 00:33:45 UTC  
> Url: https://github.com/boostorg/config/issues/467#issuecomment-1381154985  

I considered building with C++03 but Visual Studio 2022 lists C++14 as the minimum.
