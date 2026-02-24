# #130 - Including Boost Python libraries produces C2039 compiler error [Open]

> Username: b-sia  
> Created at: 2022-11-23 07:13:47 UTC  
> Updated at: 2022-11-25 11:41:34 UTC  
> Url: https://github.com/boostorg/core/issues/130  

I have built Boost_1.76 with the following configurations.  
  
```  
Compiler: MSVC 143  
Build Tools: bootstrap.bat, b2 engine  
Python Version: 3.7  
b2 Command: b2 --build-dir=build/x64 address-model=64 threading=multi --build-type=complete --stagedir=./stage/x64 -j 12  
```  
  
```cpp  
// Conversion.hpp  
#include <boost/python.hpp>        // import produces C2039  
#include <boost/python/numpy.hpp>  // import produces C2039  
#include <boost/tuple/tuple.hpp>  
#include <boost/shared_ptr.hpp>  
```  
  
```cpp  
// Conversion.cpp  
#include "Conversion.hpp" // importing the header file produces the compiler error  
```  
  
I am compiling the project with the following specs.  
  
```  
Compiler: MSVC cl.exe  
C++ Standard: ISO C++17  
Platform Toolset: Visual Studio 2022 (v143)  
```  
  
The compilation generates the following error.  
  
```  
C2039: '_copysign' is not a member of 'std'   
File: boost_1_76_python37\boost\core\cmath.hpp  
```  
  
I looked at `python.hpp `and `numpy.hpp` and could only find a reference to `std::cmath` inside `boost\python\detail\wrap_python.py`, which is used over `boost::core::cmath`. I checked the `std::cmath` header and `copysign` is neither declared nor defined. How do I make sure that my Boost library uses the `copysign` in `boost::core::cmath`?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-23 09:33:57 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1324773868  

Looks like `copysign` is defined as a macro somewhere, possibly in the Python headers. If that's the case, Python should be fixed, IMHO.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-11-23 10:25:28 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1324837654  

It might also be useful to add a workaround to Boost.Python. CC @stefanseefeld.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-23 11:25:21 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1324906506  

Check whether your `pyconfig.h` file contains `#define copysign _copysign`. I can't find such a line in my Python 3.9 installation, only a comment in `pymath.h` about it, but it might have been present in 3.7.

---

## Comment 4

> Username: b-sia  
> Created at: 2022-11-24 03:39:03 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1325910330  

@pdimov Hi Peter, I checked `pyconfig.h` and the directive is present in Python37.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-11-24 11:43:10 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1326336960  

The easiest fix then is to upgrade to a more recent Python version, one that no longer has the `#define`. I'm not sure what we can do on the Boost side to defend against that.

---

## Comment 6

> Username: b-sia  
> Created at: 2022-11-25 04:42:02 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1327015923  

@pdimov Python310 has the **#define HAVE_COPYSIGN 1** directive. Does that mean that copysign is still defined in the later Python versions.   
  
If I build my Boost library with Python310, then the compiler would not produce the **_copysign** namespace error.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-11-25 11:41:34 UTC  
> Url: https://github.com/boostorg/core/issues/130#issuecomment-1327373341  

`#define HAVE_COPYSIGN 1` in pyconfig.h just means that the compiler has a `copysign` function, which Python can use without needing to redefine `copysign` to `_copysign`.
