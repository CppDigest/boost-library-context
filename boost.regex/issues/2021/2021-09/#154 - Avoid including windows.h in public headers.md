# #154 - Avoid including windows.h in public headers [Closed]

> Username: Lastique  
> Created at: 2021-09-17 23:47:43 UTC  
> Updated at: 2021-10-06 14:46:22 UTC  
> Closed at: 2021-10-06 10:10:52 UTC  
> Url: https://github.com/boostorg/regex/issues/154  

Including `boost/regex.hpp` on Windows causes `windows.h` to be included. This is a problem because:  
- Boost.Regex also defines config macros `VC_EXTRALEAN` and `WIN32_LEAN_AND_MEAN` which are supposed to be defined (or not defined) by the user's code, not the library. In v4, they are also [defined unconditionally](https://github.com/boostorg/regex/blob/b7f44de3c54435b29e491374069873f5bc53a4d6/include/boost/regex/v4/w32_regex_traits.hpp#L40-L41), which causes warnings if the user defines them himself.  
- Including `windows.h` without also defining `_WIN32_WINNT` causes some Windows SDKs to define it to a default value (which is different for different SDKs). There is also an example of Windows SDK shipped with MSVC-8.0 which does *not* define `_WIN32_WINNT` and default to *not* providing any APIs that are conditioned on the target Windows version, which includes many very basic APIs. This causes problems in downstream libraries (e.g. Boost.Log) and user's code that wants to control the target version.  
  
The suggestion is to stop including `windows.h` in user-accessible headers. You may include it in separately compiled sources. If you need to access WinAPI in public headers, please consider these options:  
- Use Boost.WinAPI instead, as it provides its own definitions for many of Windows APIS, along with workarounds for differences between different SDKs. It's not a complete replacement for Windows SDK, but it can be extended if the needed bits are missing.  
- Provide local declarations of the missing bits of Windows SDK in Boost.Regex. You can also respect the `BOOST_USE_WINDOWS_H` macro that can be defined by user in order to force including `windows.h` instead of the local declarations. In the latter case do *not* define any config macros such as the ones mentioned above - this is user's prerogative to select the configuration of `windows.h` he wants.  
  
I'd like to also suggest including `boost/winapi/config.hpp` before `windows.h` as it will define the target Windows version that is selected by user or a Boost-wide default, and if needed, it will also define `_WIN32_WINNT` and other similar macros accordingly. The header will also define a set of macros for API partitioning, as well as version constants, etc.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-06 10:10:52 UTC  
> Url: https://github.com/boostorg/regex/issues/154#issuecomment-935901337  

Fixed in develop.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-10-06 11:38:40 UTC  
> Url: https://github.com/boostorg/regex/issues/154#issuecomment-936067645  

Thanks. I see you did the change for v5 but not v4. I'm still interested in v4, as that's the version that is used in Boost.Log. Please, also fix v4.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-06 14:35:57 UTC  
> Url: https://github.com/boostorg/regex/issues/154#issuecomment-936363480  

The v4 code is deprecated, is for C++03 only, and will be removed altogether early next year, so I'm not intending to back-port anything to it.  Boost.Log is just including boost/regex.hpp so I'm not sure why you're getting the old v4 code?  I think there may be an issue with gcc on MacOS that's still using it by mistake (BOOST_NO_CXX11_HDR_MUTEX is set due to an incomplete <mutex>), but all other modern compilers and certainly any recent flavour of msvc should be using the v5 code.

---

## Comment 4

> Username: Lastique  
> Created at: 2021-10-06 14:46:21 UTC  
> Url: https://github.com/boostorg/regex/issues/154#issuecomment-936407577  

> Boost.Log is just including boost/regex.hpp so I'm not sure why you're getting the old v4 code?  
  
Boost.Log does support C++03, so in C++03 mode it uses Boost.Regex v4.
