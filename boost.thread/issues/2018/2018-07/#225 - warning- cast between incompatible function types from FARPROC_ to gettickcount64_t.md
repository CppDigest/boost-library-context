# #225 - warning: cast between incompatible function types from FARPROC_ to gettickcount64_t [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:39:59 UTC  
> Updated at: 2018-09-06 05:11:45 UTC  
> Closed at: 2018-09-06 05:11:45 UTC  
> Url: https://github.com/boostorg/thread/issues/225  

While building 1.68.0 beta 1 on x86_64 cross-compiling to Windows 32 bits with mingw-w64 + g++ 8.2.0, I get the following warning:  
  
```  
"i686-w64-mingw32-g++-8.2.0"   -I/softs/win32-mingw-8.2.0/release/iconv/include -I/softs/win32-mingw-8.2.0/release/gettext/include -I/softs/win32-mingw-8.2.0/release/bzip2/include -I/softs/win32-mingw-8.2.0/release/zlib/include -I/softs/win32-mingw-8.2.0/release/jpeg-turbo/include -I/softs/win32-mingw-8.2.0/release/xz/include -I/softs/win32-mingw-8.2.0/release/tiff/include -I/softs/win32-mingw-8.2.0/release/png/include -std=c++14 -O2 -DNDEBUG -m32 -mthreads -O3 -finline-functions -Wno-inline -Wall -pedantic -march=i686 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_FILESYSTEM_NO_DEPRECATED -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_THREAD_WIN32 -DBOOST_USE_WINDOWS_H -DNDEBUG -DWIN32_LEAN_AND_MEAN  -I"." -c -o "bin.v2/libs/thread/build/gcc-8.2.0/release/link-static/target-os-windows/threadapi-win32/threading-multi/win32/thread_primitives.o" "libs/thread/src/win32/thread_primitives.cpp"  
libs/thread/src/win32/thread_primitives.cpp: In function 'boost::detail::win32::ticks_type boost::detail::win32::{anonymous}::get_tick_count_init()':  
libs/thread/src/win32/thread_primitives.cpp:97:120: warning: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'int (__attribute__((stdcall)) *)()'} to 'boost::detail::win32::detail::gettickcount64_t' {aka 'long long unsigned int (__attribute__((stdcall)) *)()'} [-Wcast-function-type]  
             (boost::detail::win32::detail::gettickcount64_t)boost::winapi::get_proc_address(hKernel32, "GetTickCount64");  
                                                                                                                        ^  
  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-08-06 11:35:25 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-410678958  

Hi, thanks for reporting this.  
  
It seems there is yet an issue when we moved to winapi.  
@Lastique, do you have any idea of c-what can be done?

---

## Comment 2

> Username: Lastique  
> Created at: 2018-08-06 12:17:42 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-410688425  

The only thing I can suggest is to silence or work around the warning. The code is correct, AFAICS, this is the intended usage pattern of `GetProcAddress`, which indeed requires a cast between potentially different types of function pointers.

---

## Comment 3

> Username: viboes  
> Created at: 2018-08-06 12:43:50 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-410695148  

It is weird to cast between "potentially different types of function pointers", isn't it?  
What could be the expected result of such a kind of casts?

---

## Comment 4

> Username: Lastique  
> Created at: 2018-08-06 13:00:25 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-410699705  

Not so weird, not really. `GetProcAddress` returns a pointer to some function. It can return pointers to different functions, so it has to return something that is suitable to store any pointer to function. Microsoft chose `FARPROC`, which is `int (WINAPI *)()` on 32-bit Windows. The user is supposed to know the signature of the function he requests and perform a cast (which is a nop on this platform). The result is a pointer to function with the required signature, which is bitwise equal to what `GetProcAddress` returned.  
  
This may not be a well defined standard C++, but, well, that's now 95% of system programming works.

---

## Comment 5

> Username: kivadiu  
> Created at: 2018-08-06 13:03:41 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-410700590  

So it's really harmless. I'm happy to hear that.

---

## Comment 6

> Username: viboes  
> Created at: 2018-08-06 13:51:45 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-410714944  

Ok, I would accept a PR that silent this warning.  
  
Sorry, I don't have a Windows platform to test it.

---

## Comment 7

> Username: kivadiu  
> Created at: 2018-08-09 04:49:44 UTC  
> Updated at: 2018-08-09 04:49:56 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-411635641  

Any idea why PR #227  failed some tests in appveyor?  
It seems completely unrelated to my patch.

---

## Comment 8

> Username: viboes  
> Created at: 2018-09-06 05:11:45 UTC  
> Url: https://github.com/boostorg/thread/issues/225#issuecomment-418966649  

See #227
