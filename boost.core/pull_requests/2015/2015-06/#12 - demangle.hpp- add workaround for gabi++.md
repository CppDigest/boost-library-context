# #12 demangle.hpp: add workaround for gabi++ [Closed]

> Username: bassosimone  
> Created at: 2015-06-08 22:19:00 UTC  
> Updated at: 2015-08-28 20:12:49 UTC  
> Closed at: 2015-08-28 19:32:25 UTC  
> Url: https://github.com/boostorg/core/pull/12  

Hi,  
  
Compiling [yaml-cpp](https://github.com/jbeder/yaml-cpp) for Android, I noticed that `demangle.hpp` does not compile for the mips, mips64, x86, and x86_64 targets when libc++ is used as C++ library.  
  
`demangle.hpp` does not compile with such configuration because Android's `ndk-build` tool selects as C++ ABI for such archs [gabi++](https://android.googlesource.com/platform/ndk/+/master/sources/cxx-stl/gabi++/), which lacks `abi::__cxa_demangle()` even though `cxxabi.h` exists.  
  
(Other architectures, e.g., arm, compile just fine because for them libc++abi is selected as C++ ABI.)  
  
This patch detects the case in which `cxxabi.h` is provided by gabi++ and undefines `BOOST_CORE_HAS_CXXABI_H` in `demangle.hpp` such that access to `abi::__cxa_demangle()` is not attempted.  
  
Behavior tested using the latest version of the [Android NDK](https://developer.android.com/ndk/downloads/index.html), that is, r10e. Problem also reproducible using a [minimal test case](https://github.com/bassosimone/boost-core-gabicxx-fix).  
  
Thanks.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-08-28 19:32:25 UTC  
> Url: https://github.com/boostorg/core/pull/12#issuecomment-135867675  

I did the change differently in https://github.com/boostorg/core/commit/3add966877646d0c6f43e0ec37587f52d2a757d9. Thanks.

---

## Comment 2

> Username: bassosimone  
> Created_at: 2015-08-28 20:12:49 UTC  
> Url: https://github.com/boostorg/core/pull/12#issuecomment-135877224  

Great, thanks!

---
