# #114 - make_error_code is undeclared? [Closed]

> Username: shrinktofit  
> Created at: 2023-10-03 15:52:15 UTC  
> Updated at: 2023-10-03 16:40:00 UTC  
> Closed at: 2023-10-03 16:39:59 UTC  
> Url: https://github.com/boostorg/system/issues/114  

My compiler(Visual Studio 2022) said the `make_error_code` is undeclared here:  
  
https://github.com/boostorg/system/blob/b0ef682e3d94f98478c00513ea7819f384a36f83/include/boost/system/detail/error_code.hpp#L142-L151  
  
I searched for this symbol, I saw it's defined in: `boost::system::errc` and `boost::system::windows_error` etc. It certainly cannot be found within `boost::system` namespace(L150).

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-03 15:54:53 UTC  
> Url: https://github.com/boostorg/system/issues/114#issuecomment-1745264272  

What program are you trying to compile?

---

## Comment 2

> Username: shrinktofit  
> Created at: 2023-10-03 16:09:33 UTC  
> Updated at: 2023-10-03 16:11:30 UTC  
> Url: https://github.com/boostorg/system/issues/114#issuecomment-1745294366  

I'm writing a program involving boost.beast, the boost.beast is referring to boost.system via:  
  
![image](https://github.com/boostorg/system/assets/28807867/5e135038-c95a-40b2-814a-b2bd747cc775)  
  
and then the above error is given:  
  
```  
out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\system\detail\error_code.hpp(150): error C3861: “make_error_code”: Could not find the identifier.  
```  
  
My IDE is Visual Studio 2022, C++ standard is configured as C++20 and I'm using C++ modules.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-10-03 16:16:21 UTC  
> Url: https://github.com/boostorg/system/issues/114#issuecomment-1745307613  

The `make_error_code` overloads for `boost::asio::error` are here: https://github.com/boostorg/asio/blob/28ff1e7c2b44f141eb809abaf2d76c95f38350ca/include/boost/asio/error.hpp#L334-L356  
  
`make_error_code(e)` should find them via argument-dependent lookup.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-10-03 16:18:46 UTC  
> Url: https://github.com/boostorg/system/issues/114#issuecomment-1745311988  

E.g. https://godbolt.org/z/xco7hY5PP

---

## Comment 5

> Username: shrinktofit  
> Created at: 2023-10-03 16:39:59 UTC  
> Url: https://github.com/boostorg/system/issues/114#issuecomment-1745348521  

Thank you @pdimov  . I must have encountered some language specific problem. Let me digit into it. :)
