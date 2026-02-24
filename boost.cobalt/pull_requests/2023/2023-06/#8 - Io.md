# #8 Io [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-06-30 15:48:09 UTC  
> Updated at: 2025-06-21 03:43:44 UTC  
> Closed at: 2025-06-21 03:43:44 UTC  
> Url: https://github.com/boostorg/cobalt/pull/8  



---

## Comment 1

> Username: jsaf0  
> Created_at: 2023-07-10 18:55:22 UTC  
> Url: https://github.com/boostorg/cobalt/pull/8#issuecomment-1629531811  

See #13.

---

## Comment 2

> Username: jsaf0  
> Created_at: 2023-07-10 18:56:09 UTC  
> Url: https://github.com/boostorg/cobalt/pull/8#issuecomment-1629533586  

The Jamfile is not updated with the new .cpp files, so the all the new io things are not built in the CI jobs. The same goes for the new io tests.

---

## Comment 3

> Username: jsaf0  
> Created_at: 2023-07-10 19:00:03 UTC  
> Url: https://github.com/boostorg/cobalt/pull/8#issuecomment-1629543357  

Building on Ubuntu 23.04 with gcc 12.2 against Boost 1.82 yields the following compile error when compiling `popen.cpp`:  
  
```  
[  0%] Building CXX object CMakeFiles/boost_async.dir/src/io/popen.cpp.o  
In file included from /data/work/async/include/boost/async/io/process.hpp:17,  
                 from /data/work/async/include/boost/async/io/popen.hpp:12,  
                 from /data/work/async/src/io/popen.cpp:8:  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp:1377:50: error: template argument 1 is invalid  
 1377 |                     detail::native_handle_deleter> handle_{environment::detail::load_native_handle()};  
      |                                                  ^  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp:1377:50: error: template argument 2 is invalid  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp:1376:8: error: ‘<expression error>’ in namespace ‘std’ does not name a type  
 1376 |   std::unique_ptr<typename remove_pointer<native_handle_type>::type,  
      |        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
 1377 |                     detail::native_handle_deleter> handle_{environment::detail::load_native_handle()};  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp: In member function ‘const char* const* boost::process::v2::environment::current_view::native_handle()’:  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp:1333:50: error: ‘handle_’ was not declared in this scope  
 1333 |     native_handle_type  native_handle() { return handle_.get(); }  
      |                                                  ^~~~~~~  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp: In member function ‘boost::process::v2::environment::current_view::iterator boost::process::v2::environment::current_view::begin() const’:  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp:1371:45: error: ‘handle_’ was not declared in this scope  
 1371 |     iterator begin() const {return iterator(handle_.get());}  
      |                                             ^~~~~~~  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp: In member function ‘boost::process::v2::environment::current_view::iterator boost::process::v2::environment::current_view::end() const’:  
/home/jfp/.local/boost-1.82/include/boost/process/v2/environment.hpp:1372:62: error: ‘handle_’ was not declared in this scope  
 1372 |     iterator   end() const {return iterator(detail::find_end(handle_.get()));}  
      |                                                              ^~~~~~~  
make[2]: *** [CMakeFiles/boost_async.dir/build.make:510: CMakeFiles/boost_async.dir/src/io/popen.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:169: CMakeFiles/boost_async.dir/all] Error 2  
make: *** [Makefile:136: all] Error 2  
```

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-07-14 07:59:24 UTC  
> Url: https://github.com/boostorg/cobalt/pull/8#issuecomment-1635459891  

This won't be merged anytime soon. Consider it a poc and preview of what might come in the future.

---
