# #333 Fix compilation with -Wall and -Werror [Merged]

> Username: Kalabaza  
> Created at: 2023-08-10 12:08:45 UTC  
> Updated at: 2023-08-14 09:41:00 UTC  
> Merged at: 2023-08-14 09:41:00 UTC  
> Closed at: 2023-08-14 09:41:00 UTC  
> Url: https://github.com/boostorg/process/pull/333  

While compiling with gcc 11.4 using -Wall and -Werror, this warning stops the compilation:  
  
build/_deps/boost-src/libs/process/include/boost/process/detail/posix/async_out.hpp:74:38: error: unused parameter ‘exec’ [-Werror=unused-parameter]  
   74 |     inline void on_success(Executor &exec)  
      |                            ~~~~~~~~~~^~~~  
cc1plus: all warnings being treated as errors

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-08-10 12:14:48 UTC  
> Url: https://github.com/boostorg/process/pull/333#issuecomment-1673105296  

If everything passes I'll merge after the boost release.

---
