# #38 - memory_resource is still experimental in libc++ [Closed]

> Username: nejati-deriv  
> Created at: 2023-01-02 08:25:00 UTC  
> Updated at: 2023-01-30 07:03:47 UTC  
> Closed at: 2023-01-30 07:03:47 UTC  
> Url: https://github.com/boostorg/redis/issues/38  

`libc++` doesn't have an `memory_resource` include file at this moment and we have to use `<experimental/memory_resource>`.  
It is going to be fixed in clang-16 though [Release Note](https://libcxx.llvm.org/ReleaseNotes.html#what-s-new-in-libc-16-0-0).

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-01-02 20:43:39 UTC  
> Url: https://github.com/boostorg/redis/issues/38#issuecomment-1369194655  

I am wondering if Aedis should use `#include <boost/json/memory_resource.hpp>` instead of `std`. I have to sepup CI for `libc++`.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-01-29 20:15:45 UTC  
> Url: https://github.com/boostorg/redis/issues/38#issuecomment-1407759765  

Memory resource has been removed https://github.com/boostorg/redis/pull/57.
