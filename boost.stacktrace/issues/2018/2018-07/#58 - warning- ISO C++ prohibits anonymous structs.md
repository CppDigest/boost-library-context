# #58 - warning: ISO C++ prohibits anonymous structs [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:43:19 UTC  
> Updated at: 2018-08-08 21:35:57 UTC  
> Closed at: 2018-08-08 21:35:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/58  

While building 1.68.0 beta 1 on x86_64 linux cross-compiling to Windows 64 bits with minw-w64 + g++ 8.2.0, I get this warning:  
  
```  
"x86_64-w64-mingw32-g++-8.2.0"   -I/softs/win64-mingw-8.2.0/release/iconv/include -I/softs/win64-mingw-8.2.0/release/gettext/include -I/softs/win64-mingw-8.2.0/release/bzip2/include -I/softs/win64-mingw-8.2.0/release/zlib/include -I/softs/win64-mingw-8.2.0/release/jpeg-turbo/include -I/softs/win64-mingw-8.2.0/release/xz/include -I/softs/win64-mingw-8.2.0/release/tiff/include -I/softs/win64-mingw-8.2.0/release/png/include -std=c++14 -O2 -DNDEBUG -m64 -mthreads -O3 -finline-functions -Wno-inline -Wall -pedantic  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_FILESYSTEM_NO_DEPRECATED -DBOOST_LOG_WITHOUT_EVENT_LOG -DNDEBUG  -I"." -c -o "bin.v2/libs/stacktrace/build/gcc-8.2.0/release/link-static/target-os-windows/threading-multi/basic.o" "libs/stacktrace/build/../src/basic.cpp"  
In file included from ./boost/winapi/file_management.hpp:17,  
                 from ./boost/stacktrace/detail/safe_dump_win.ipp:20,  
                 from ./boost/stacktrace/safe_dump_to.hpp:210,  
                 from ./boost/stacktrace/frame.hpp:20,  
                 from ./boost/stacktrace/detail/frame_unwind.ipp:15,  
                 from libs/stacktrace/build/../src/basic.cpp:14:  
./boost/winapi/overlapped.hpp:38:9: warning: ISO C++ prohibits anonymous structs [-Wpedantic]  
         };  
         ^  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-08-07 19:33:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/58#issuecomment-411175074  

@Lastique looks like this issue belongs to Boost.WinAPI  
My guess is that [_OVERLAPPED](https://github.com/boostorg/winapi/blob/develop/include/boost/winapi/overlapped.hpp#L34-L40) requires two ```BOOST_WINAPI_DETAIL_EXTENSION```

---

## Comment 2

> Username: Lastique  
> Created at: 2018-08-08 17:54:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/58#issuecomment-411495578  

See if https://github.com/boostorg/winapi/commit/fee1bec7f97c24d082ea0b1300aba83c3899484e fixes the problem.

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-08-08 18:21:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/58#issuecomment-411503784  

Yes boostorg/winapi@fee1bec fixes the issue.  
Thanks

---

## Comment 4

> Username: apolukhin  
> Created at: 2018-08-08 21:35:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/58#issuecomment-411560318  

@kivadiu many thanks for reporting the issue!  
@Lastique even more thanks for the fix!
