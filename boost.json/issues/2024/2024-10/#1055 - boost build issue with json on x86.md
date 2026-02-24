# #1055 - boost build issue with json on x86 [Closed]

> Username: dharanlinux  
> Created at: 2024-10-15 13:43:23 UTC  
> Updated at: 2024-12-14 11:08:54 UTC  
> Closed at: 2024-12-14 11:08:53 UTC  
> Url: https://github.com/boostorg/json/issues/1055  

Facing below issue, while build with json module for x86 platform.  
  
**Error Log:**  
  
```  
gcc.compile.c++ bin.v2/libs/json/build/gcc-7.5.0/release/x86_64/threading-multi/visibility-hidden/src.o  
In file included from ./boost/json/detail/charconv/detail/fast_float/fast_float.hpp:11:0,  
                 from ./boost/json/detail/charconv/impl/from_chars.ipp:14,  
                 from ./boost/json/src.hpp:58,  
                 from libs/json/src/src.cpp:10:  
./boost/json/detail/charconv/detail/fast_float/float_common.hpp:306:73: error: conflicting declaration 'constexpr const uint64_t boost::json::detail::charconv::detail::fast_float::binary_format_lookup_tables<double, U>::max_mantissa []'  
 constexpr uint64_t binary_format_lookup_tables<double, U>::max_mantissa[];  
                                                                         ^  
In file included from ./boost/json/detail/charconv/detail/fast_float/fast_float.hpp:11:0,  
                 from ./boost/json/detail/charconv/impl/from_chars.ipp:14,  
                 from ./boost/json/src.hpp:58,  
                 from libs/json/src/src.cpp:10:  
./boost/json/detail/charconv/detail/fast_float/float_common.hpp:275:34: note: previous declaration as 'constexpr const uint64_t boost::json::detail::charconv::detail::fast_float::binary_format_lookup_tables<double, U>::max_mantissa [24]'  
   static constexpr std::uint64_t max_mantissa[] = {  
                                  ^~~~~~~~~~~~  
In file included from ./boost/json/detail/charconv/detail/fast_float/fast_float.hpp:11:0,  
                 from ./boost/json/detail/charconv/impl/from_chars.ipp:14,  
                 from ./boost/json/src.hpp:58,  
                 from libs/json/src/src.cpp:10:  
./boost/json/detail/charconv/detail/fast_float/float_common.hpp:306:73: error: declaration of 'constexpr const uint64_t boost::json::detail::charconv::detail::fast_float::binary_format_lookup_tables<double, U>::max_mantissa [24]' outside of class is not definition [-fpermissive]  
 constexpr uint64_t binary_format_lookup_tables<double, U>::max_mantissa[];  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-16 16:12:36 UTC  
> Url: https://github.com/boostorg/json/issues/1055#issuecomment-2417292017  

Please also tell the Boost version and the compilation flags used. In order to see the build flags build with `b2 -d+2`.

---

## Comment 2

> Username: dharanlinux  
> Created at: 2024-10-17 07:27:42 UTC  
> Updated at: 2024-10-17 07:30:00 UTC  
> Url: https://github.com/boostorg/json/issues/1055#issuecomment-2418772407  

Please find the below details:  
  
```  
  
    "/home/dharan/build/toolchain/bin/x86_64-linux-uclibc-g++"   -fvisibility-inlines-hidden -Os -g -mtune=btver2 -pipe -std=c++11 -fpermissive -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_JSON_DYN_LINK=1 -DBOOST_JSON_SOURCE -DNDEBUG   -I"."  -c -o "bin.v2/libs/json/build/gcc-5.5.0/release/threading-multi/visibility-hidden/src.o" "libs/json/src/src.cpp"  
  
In file included from ./boost/json/detail/charconv/detail/fast_float/fast_float.hpp:11:0,  
                 from ./boost/json/detail/charconv/impl/from_chars.ipp:14,  
                 from ./boost/json/src.hpp:58,  
                 from libs/json/src/src.cpp:10:  
./boost/json/detail/charconv/detail/fast_float/float_common.hpp:306:73: error: conflicting declaration 'constexpr const uint64_t boost::json::detail::charconv::detail::fast_float::binary_format_lookup_tables<double, U>::max_mantissa []'  
 constexpr uint64_t binary_format_lookup_tables<double, U>::max_mantissa[];  
                                                                         ^  
In file included from ./boost/json/detail/charconv/detail/fast_float/fast_float.hpp:11:0,  
                 from ./boost/json/detail/charconv/impl/from_chars.ipp:14,  
                 from ./boost/json/src.hpp:58,  
                 from libs/json/src/src.cpp:10:  
./boost/json/detail/charconv/detail/fast_float/float_common.hpp:275:34: note: previous declaration as 'constexpr const uint64_t boost::json::detail::charconv::detail::fast_float::binary_format_lookup_tables<double, U>::max_mantissa [24]'  
   static constexpr std::uint64_t max_mantissa[] = {  
                                  ^  
In file included from ./boost/json/detail/charconv/detail/fast_float/fast_float.hpp:11:0,  
                 from ./boost/json/detail/charconv/impl/from_chars.ipp:14,  
                 from ./boost/json/src.hpp:58,  
                 from libs/json/src/src.cpp:10:  
./boost/json/detail/charconv/detail/fast_float/float_common.hpp:306:73: warning: declaration of 'constexpr const uint64_t boost::json::detail::charconv::detail::fast_float::binary_format_lookup_tables<double, U>::max_mantissa [24]' outside of class is not definition [-fpermissive]  
 constexpr uint64_t binary_format_lookup_tables<double, U>::max_mantissa[];  
                                                                         ^  
...skipped <pbin.v2/libs/json/build/gcc-5.5.0/release/threading-multi/visibility-hidden>libboost_json.so.1.85.0 for lack of <pbin.v2/libs/json/build/gcc-5.5.0/release/threading-multi/visibility-hidden>src.o...  
...skipped <p/home/dharan/build/toolchain/usr/lib>libboost_json.so.1.85.0 for lack of <pbin.v2/libs/json/build/gcc-5.5.0/release/threading-multi/visibility-hidden>libboost_json.so.1.85.0...  
gcc.link.dll bin.v2/libs/iostreams/build/gcc-5.5.0/release/threading-multi/visibility-hidden/libboost_iostreams.so.1.85.0  
```  
  
Currently, I am using the boost-1.85.0 and I tried with 1.86.0 also. Both have same build issue.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-10-17 10:53:48 UTC  
> Url: https://github.com/boostorg/json/issues/1055#issuecomment-2419202827  

It looks like you are hitting a compiler bug. For example, see https://godbolt.org/z/fEsdvPzov. Also, the first snippet had gcc-5.5.0 in build path, and the second has gcc-7.5.0. Which GCC version are you actually using?

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-12-14 11:08:53 UTC  
> Url: https://github.com/boostorg/json/issues/1055#issuecomment-2543059457  

Closing this, since there was no responce.
