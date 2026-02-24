# #92 - Build using boost 1.80 fails, works with 1.79 [Closed]

> Username: jcbastosportela  
> Created at: 2022-09-07 15:17:19 UTC  
> Updated at: 2022-09-09 08:20:10 UTC  
> Closed at: 2022-09-09 08:20:10 UTC  
> Url: https://github.com/boostorg/system/issues/92  

From boost 1.79 this change starts causing the build to fail in our target system (B&R PLC).  
  
This platform does not have threading, resulting in `BOOST_NO_CXX11_HDR_MUTEX` to be defined (in `boost/config/stdlib/libstdcpp3.hpp`).  
  
In boost 1.80 `boost/system/detail/config.hpp` starts considering this for defining `BOOST_SYSTEM_HAS_SYSTEM_ERROR` and this is now not defined (in 1.79 was).  
  
This causes the compilation error with gcc6.3, c++14 (simplified output):  
```  
...\boost\json\impl\parse.ipp:   :In function 'boost::json::value boost::json::parse(boost::json::string_view, std::error_code&, boost::json::storage_ptr, const boost::json::parse_options&)'  
...\boost\json\impl\parse.ipp: (Ln: 46, Col: 10) error :no match for 'operator=' in 'ec = jec' (operand types are 'std::error_code' and 'boost::json::error_code {aka boost::system::error_code}')  
...  
```  
  
_NOTE_ forcing the flag `BOOST_SYSTEM_HAS_SYSTEM_ERROR` will result in this error to be fixed, but then starts failing in `boost\system\detail\error_category_impl.hpp` because `mutex` is not defined (remember that our target does not have threading enabled) and also in boost 1.80 when `BOOST_SYSTEM_HAS_SYSTEM_ERROR` is set `std::mutex` is used (was not in boost 1.79).  
  
So, it seems that with the current configuration of boost, a target that has std::error but does not have std::mutex is not supported.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-09-07 15:52:44 UTC  
> Url: https://github.com/boostorg/system/issues/92#issuecomment-1239578313  

One possible workaround on your side would be to supply a dummy `<mutex>` header that has `std::mutex` and `std::lock_guard` that do nothing.  
  
The other option is for me to allow configuration via a macro, `BOOST_SYSTEM_DISABLE_THREADS`, that turns off the use of the mutex.

---

## Comment 2

> Username: jcbastosportela  
> Created at: 2022-09-08 07:24:15 UTC  
> Url: https://github.com/boostorg/system/issues/92#issuecomment-1240329775  

@pdimov thank you for the fast reply. Supplying a dummy `<mutex>` is something that occurred to me, however that is not very practical because the toolchain of our platform does have the `<mutex>` (in this same exact version: https://github.com/gcc-mirror/gcc/blob/releases/gcc-6.3.0/libstdc++-v3/include/std/mutex); however `_GLIBCXX_HAS_GTHREADS` is not defined, so mutex is not defined.  
  
I could play with include paths and provide my own version of the `<mutex>` but that is something that I want to avoid for now. Meanwhile I can keep using boost 1.79.  
  
How feasible is the second option? Do you think it is something we could see in for example boost 1.81 or boost 1.82? Or is our case something atypical that boost won't be willing to cover?

---

## Comment 3

> Username: pdimov  
> Created at: 2022-09-08 17:41:47 UTC  
> Url: https://github.com/boostorg/system/issues/92#issuecomment-1241033110  

Support for `BOOST_SYSTEM_DISABLE_THREADS` added in https://github.com/boostorg/system/commit/53c00841fc0d892bf43cda60e3ea2f05c4362b32. You can apply this patch on top of 1.80, or wait for 1.81.

---

## Comment 4

> Username: jcbastosportela  
> Created at: 2022-09-09 08:20:10 UTC  
> Url: https://github.com/boostorg/system/issues/92#issuecomment-1241659354  

This seems to fix the issue for me! Thanks @pdimov
