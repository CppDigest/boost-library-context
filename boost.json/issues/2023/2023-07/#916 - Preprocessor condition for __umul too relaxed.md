# #916 - Preprocessor condition for __umul too relaxed [Closed]

> Username: gjasny  
> Created at: 2023-07-13 08:23:33 UTC  
> Updated at: 2023-07-26 12:48:05 UTC  
> Closed at: 2023-07-26 12:48:05 UTC  
> Url: https://github.com/boostorg/json/issues/916  

Hello,  
  
when trying to compile Boost 1.83.0-beta1 I noticed an compilation error:  
  
```  
    "clang++"   -O3 -Wall -fvisibility=default -Wno-inline -g -std=c++17 -stdlib=libc++  -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS16.4.sdk -arch armv7 -arch arm64 -miphoneos-version-min=10.0 -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1 -DBOOST_JSON_SOURCE -DBOOST_JSON_STATIC_LINK=1 -DNDEBUG   -I"."  -c -o "/Users/gregorj/Git/el/boost/_output/iphoneos/boost/bin.v2/libs/json/build/clang-darwin-14/release/address-model-32_64/link-static/pch-off/target-os-iphone/threading-multi/visibility-global/src.o" "libs/json/src/src.cpp"  
  
In file included from libs/json/src/src.cpp:10:  
In file included from ./boost/json/src.hpp:58:  
In file included from ./boost/json/detail/charconv/impl/from_chars.ipp:15:  
In file included from ./boost/json/detail/charconv/detail/from_chars_float_impl.hpp:12:  
In file included from ./boost/json/detail/charconv/detail/compute_float64.hpp:11:  
./boost/json/detail/charconv/detail/emulated128.hpp:85:26: error: use of undeclared identifier '__umulh'  
    std::uint64_t high = __umulh(x, y);  
                         ^  
1 error generated.  
```  
  
This happened because my compilation scripts still include(d) a 32bit ARM target (`-arch armv7`).  
  
I guess this check here:  
https://github.com/boostorg/json/blob/0a7860fcfce7d66c0abe3d96f666540c00c33f73/include/boost/json/detail/charconv/detail/emulated128.hpp#L82-L87  
  
Must be tightened to only include 64bit ARM.  
  
Thanks,  
Gregor

---

## Comment 1

> Username: pdimov  
> Created at: 2023-07-13 08:29:49 UTC  
> Url: https://github.com/boostorg/json/issues/916#issuecomment-1633800681  

@mborland

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-07-23 21:34:56 UTC  
> Url: https://github.com/boostorg/json/issues/916#issuecomment-1646965044  

@gjasny can you check if #922 fixes the issue?

---

## Comment 3

> Username: pdimov  
> Created at: 2023-07-23 21:45:39 UTC  
> Url: https://github.com/boostorg/json/issues/916#issuecomment-1646967039  

Since `__umulh` is an [MSVC intrinsic](https://learn.microsoft.com/en-us/cpp/intrinsics/umulh?view=msvc-170), the correct macro to check should be the [MSVC-specific `_M_ARM64`](https://learn.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=msvc-170), not the GCC one.

---

## Comment 4

> Username: gjasny  
> Created at: 2023-07-25 19:12:15 UTC  
> Url: https://github.com/boostorg/json/issues/916#issuecomment-1650390316  

> @gjasny can you check if #922 fixes the issue?  
  
Thanks for the fix. I’m traveling right now and won’t be able to verify your patch before the upcoming Boost release.
