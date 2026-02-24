# #929 - Issue 774 on ARM [Open]

> Username: beewoolie  
> Created at: 2023-08-14 17:51:34 UTC  
> Updated at: 2023-08-21 03:06:07 UTC  
> Url: https://github.com/boostorg/json/issues/929  

We're seeing the same issue (#774) with the following invocation of the ARM cross compiler.  We're using v1.83 of boost.  
  
We tried to find a way to work around it, but none of our attempts succeeded.  
  
```  
/opt/arm-gnu-toolchain-12.2.rel1-darwin-arm64-arm-none-eabi/bin/arm-none-eabi-g++ -c -mcpu=cortex-m4 -mthumb -mno-unaligned-access  -mfloat-abi=soft  -fdata-sections -ffunction-sections -Wno-unused-function -fno-exceptions  -Iext/boost/boost -Os -g3 -Wall -Wundef -DOS=OS_MACOS -fdiagnostics-color=always -fno-rtti --std=gnu++20 -Wno-literal-suffix -Wno-dangling-pointer -fno-threadsafe-statics  -o ../o/lib-glow/jsonito.o generics/jsonito.cc  
In file included from ext/boost/boost/boost/json/src.hpp:37,  
                 from generics/jsonito.cc:43:  
ext/boost/boost/boost/json/impl/parse.ipp: In function 'boost::json::value boost::json::parse(string_view, std::error_code&, storage_ptr, const parse_options&)':  
ext/boost/boost/boost/json/impl/parse.ipp:49:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
   49 |     ec = jec;  
      |          ^~~  
In file included from /opt/arm-gnu-toolchain-12.2.rel1-darwin-arm64-arm-none-eabi/arm-none-eabi/include/c++/12.2.1/bits/ios_base.h:46,  
                 from /opt/arm-gnu-toolchain-12.2.rel1-darwin-arm64-arm-none-eabi/arm-none-eabi/include/c++/12.2.1/ios:42,  
                 from ext/boost/boost/boost/core/detail/string_view.hpp:30,  
                 from ext/boost/boost/boost/json/string_view.hpp:14,  
                 from ext/boost/boost/boost/json/kind.hpp:14,  
                 from ext/boost/boost/boost/json/array.hpp:14,  
                 from ext/boost/boost/boost/json.hpp:15,  
                 from ext/boost/boost/boost/json/src.hpp:27:  
/opt/arm-gnu-toolchain-12.2.rel1-darwin-arm64-arm-none-eabi/arm-none-eabi/include/c++/12.2.1/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
  225 |       operator=(_ErrorCodeEnum __e) noexcept  
      |       ^~~~~~~~  
  
  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-08-15 07:18:39 UTC  
> Url: https://github.com/boostorg/json/issues/929#issuecomment-1678520023  

This appears to be another problem with detection of platform features on MacOS. @pdimov can you look into it?

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-08-15 11:35:31 UTC  
> Url: https://github.com/boostorg/json/issues/929#issuecomment-1678788479  

Just to be sure, have you tried any of the workarounds mentioned in #774 ?

---

## Comment 3

> Username: pdimov  
> Created at: 2023-08-15 14:11:34 UTC  
> Url: https://github.com/boostorg/json/issues/929#issuecomment-1679001460  

I suppose this is again caused by `BOOST_NO_CXX11_HDR_MUTEX` being defined by Boost.Config for this platform, but I've no idea why that is.  
  
Is libstdc++ compiled as single-threaded?

---

## Comment 4

> Username: beewoolie  
> Created at: 2023-08-20 23:15:00 UTC  
> Url: https://github.com/boostorg/json/issues/929#issuecomment-1685421630  

Likely.  These are arm-none-eabi targets, no OS, single core, so there isn't going to be a need for threading.    
  
I don't have a working sample anymore since I had to move on.  I don't have a way to test the boost JSON code and compiler switches.  
  
FWIW, nlohmann's library links and it doesn't require additional code when we disable exceptions.  Perhaps at a later date we can return to this detail and determine whether or not the boost library is a better long-term component.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-08-21 03:06:07 UTC  
> Url: https://github.com/boostorg/json/issues/929#issuecomment-1685554721  

In that case, the solution is to define the macro BOOST_SYSTEM_DISABLE_THREADS.
