# #57 Avoid implicit cast from size_t to uint32_t [Merged]

> Username: travbid  
> Created at: 2022-11-07 05:12:51 UTC  
> Updated at: 2023-01-17 12:28:04 UTC  
> Merged at: 2023-01-17 12:27:49 UTC  
> Closed at: 2023-01-17 12:27:49 UTC  
> Url: https://github.com/boostorg/dll/pull/57  

Hello, we have an error on line 42 of `path_from_handle.hpp`.    
With the XCode 14.1 update, the `-Wshorten-64-to-32` flag has somehow been enabled in our project and shows this warning when including `#include "boost/dll/import.hpp"`  
```  
In file included from ***/boost/dll/import.hpp:16:  
In file included from ***/boost/dll/shared_library.hpp:26:  
In file included from ***/boost/dll/detail/posix/shared_library_impl.hpp:13:  
***/boost/dll/detail/posix/path_from_handle.hpp:42:59: error: implicit conversion loses integer precision: 'std::size_t' (aka 'unsigned long') to 'uint32_t' (aka 'unsigned int') [-Werror,-Wshorten-64-to-32]  
const char* image_name = _dyld_get_image_name(i);  
~~~~~~~~~~~~~~~~~~~~ ^  
1 error generated.  
```  
According to the [linked docs](https://web.archive.org/web/20150923111543/https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man3/dyld.3.html) on line 37, `_dyld_get_image_name()` takes a `uint32_t` but here is passed a `size_t`. With `-Wconversion` enabled this creates a warning when `size_t` is a larger type than `uint32_t`.  
`_dyld_image_count()` returns a `uint32_t` so I think this can be solved by not casting to `size_t` in the first place.    
  
Edit: I removed the `std::` prefix because `mach-o/dyld.h` has the type un-prefixed and it seems `uint32_t` is not part of the C++03 standard.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2023-01-17 12:28:03 UTC  
> Url: https://github.com/boostorg/dll/pull/57#issuecomment-1385351581  

Many thanks for the PR!

---
