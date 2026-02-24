# #400 - close_range is missing on FreeBSD 10 [Open]

> Username: alexsharoff  
> Created at: 2024-09-12 08:38:02 UTC  
> Updated at: 2025-03-11 03:28:43 UTC  
> Url: https://github.com/boostorg/process/issues/400  

We're trying to build Boost 1.86.0 for FreeBSD 10 and getting the following error:  
  
```  
".../i686-pc-freebsd10.0_gcc7.3.0/bin/i686-pc-freebsd10.0-g++"   -fvisibility-inlines-hidden -static-libgcc -static-libstdc++ -static-libgcc -static-libstdc++ -std=gnu++14 -fPIC   -O2 -DNDEBUG -DBOOST_CHRONO_THREAD_DISABLED -m32 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o ".../boost/bin.v2/libs/process/build/gcc-7/release/x86_32/link-static/target-os-freebsd/threading-multi/visibility-hidden/posix/close_handles.o" "libs/process/src/posix/close_handles.cpp"  
  
libs/process/src/posix/close_handles.cpp: In function 'void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)':  
libs/process/src/posix/close_handles.cpp:125:15: error: '::close_range' has not been declared  
             ::close_range(0, whitelist.front() - 1, 0);  
               ^~~~~~~~~~~  
libs/process/src/posix/close_handles.cpp:125:15: note: suggested alternative: 'close_all'  
             ::close_range(0, whitelist.front() - 1, 0);  
               ^~~~~~~~~~~  
               close_all  
libs/process/src/posix/close_handles.cpp:135:19: error: '::close_range' has not been declared  
                 ::close_range(mine + 1, next - 1, 0);  
                   ^~~~~~~~~~~  
libs/process/src/posix/close_handles.cpp:135:19: note: suggested alternative: 'close_all'  
                 ::close_range(mine + 1, next - 1, 0);  
                   ^~~~~~~~~~~  
                   close_all  
```  
  
It seems this is due to code that is used only in Process V2. Is there a way to fallback to Process V1?

---

## Comment 1

> Username: o01eg  
> Created at: 2025-03-11 03:28:42 UTC  
> Url: https://github.com/boostorg/process/issues/400#issuecomment-2712479292  

It also affects musl C library on Linux:  
  
```  
2025-03-10T17:31:30.3731617Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp: In function 'void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)':  
2025-03-10T17:31:30.3733635Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:128:15: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-10T17:31:30.3734995Z   128 |             ::close_range(0, whitelist.front() - 1, CLOSE_RANGE_UNSHARE);  
2025-03-10T17:31:30.3735547Z       |               ^~~~~~~~~~~  
2025-03-10T17:31:30.3735932Z       |               copy_range  
2025-03-10T17:31:30.3736905Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:138:19: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-10T17:31:30.3738024Z   138 |                 ::close_range(mine + 1, next - 1, CLOSE_RANGE_UNSHARE);  
2025-03-10T17:31:30.3738553Z       |                   ^~~~~~~~~~~  
2025-03-10T17:31:30.3738955Z       |                   copy_range  
2025-03-10T17:31:30.3739940Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:142:11: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-10T17:31:30.3741217Z   142 |         ::close_range(whitelist.back() + 1, std::numeric_limits<int>::max(), CLOSE_RANGE_UNSHARE);  
2025-03-10T17:31:30.3741880Z       |           ^~~~~~~~~~~  
2025-03-10T17:31:30.3742243Z       |           copy_range  
2025-03-10T17:31:30.3743206Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:145:11: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-10T17:31:30.3744398Z   145 |         ::close_range(0, std::numeric_limits<int>::max(), CLOSE_RANGE_UNSHARE);  
2025-03-10T17:31:30.3745155Z       |           ^~~~~~~~~~~  
2025-03-10T17:31:30.3745515Z       |           copy_range  
```
