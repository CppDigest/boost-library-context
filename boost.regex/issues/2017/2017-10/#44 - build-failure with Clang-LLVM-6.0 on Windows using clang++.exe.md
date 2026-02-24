# #44 - build-failure with Clang/LLVM-6.0 on Windows using clang++.exe [Closed]

> Username: degski  
> Created at: 2017-10-17 05:38:35 UTC  
> Updated at: 2018-07-21 16:49:12 UTC  
> Closed at: 2018-07-21 16:49:12 UTC  
> Url: https://github.com/boostorg/regex/issues/44  

config.hpp:166: `#if defined(__GNUC__) && (defined(_WIN32) || defined(__CYGWIN__))` causes build-failure, due to fact that clang defines `__GNUC__` *and* `_WIN32`, which implies MinGW or Cygwin, and therefore includes not available dirent.h in fileiter.hpp:76.   
  
changing config.hpp:166 to: `#if defined(__GNUC__) && !defined(__clang__) && (defined(_WIN32) || defined(__CYGWIN__))`  
  
allows regex to build with Clang/LLVM-6.0 on Windows using clang++.exe.  
  
EDIT: Still not a final solution, as this leaves Windows + Clang + MinGW in the lurch...

---

## Comment 1

> Username: degski  
> Created at: 2017-10-26 12:18:08 UTC  
> Url: https://github.com/boostorg/regex/issues/44#issuecomment-339647224  

If [this](http://lists.llvm.org/pipermail/cfe-users/2017-October/001203.html) is correct,   
  
changing config.hpp:166 to: `#if defined(__GNUC__) && !defined(_MSC_VER) && (defined(_WIN32) || defined(__CYGWIN__))`  
  
will be the solution.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-07-21 16:49:12 UTC  
> Url: https://github.com/boostorg/regex/issues/44#issuecomment-406809143  

Assuming this is fixed now?
