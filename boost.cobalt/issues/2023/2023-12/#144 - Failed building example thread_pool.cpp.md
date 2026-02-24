# #144 - Failed building example thread_pool.cpp [Closed]

> Username: assafcohen  
> Created at: 2023-12-19 10:43:30 UTC  
> Updated at: 2023-12-27 18:38:55 UTC  
> Closed at: 2023-12-27 18:38:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/144  

while trying to build https://github.com/boostorg/cobalt/blob/master/example/thread_pool.cpp  
  
os = Macos  
compiler = apple-clang  
compiler version = 15.0  
compiler cppstd = 23  
compiler libcxx = libc++  
  
```  
====================[ Build | toy_app | osx-ninja-debug ]=======================  
"/Users/***/Applications/CLion Nova.app/Contents/bin/cmake/mac/aarch64/bin/cmake" --build /Users/***/Documents/metrics/cmake-build-osx-ninja-debug --target toy_app -j 10  
[1/2] Building CXX object apps/toy/CMakeFiles/toy_app.dir/main.cpp.o  
FAILED: apps/toy/CMakeFiles/toy_app.dir/main.cpp.o   
/usr/bin/c++ -DBOOST_ASIO_HAS_CO_AWAIT -DBOOST_ASIO_HAS_FILE -DBOOST_ASIO_HAS_STD_COROUTINE -DBOOST_FILESYSTEM_NO_DEPRECATED -DBOOST_SYSTEM_NO_DEPRECATED -DPROJECT_NAME=\"toy\" -DRESOURCES_DIRECTORY_NAME=\"resources\" -D__DARWIN__ -D__arm64__ -Dtoy_VERSION="v0.0.1 (build 9999), Copyright 2023 " -I/Users/***/Documents/metrics/apps/toy/src -I/Users/***/Documents/metrics/apps/toy/src/Darwin -isystem /Users/***/***/include -stdlib=libc++ -g -std=gnu++2b -arch arm64 -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.2.sdk -fcolor-diagnostics -MD -MT apps/toy/CMakeFiles/toy_app.dir/main.cpp.o -MF apps/toy/CMakeFiles/toy_app.dir/main.cpp.o.d -o apps/toy/CMakeFiles/toy_app.dir/main.cpp.o -c /Users/***/Documents/metrics/apps/toy/main.cpp  
/Users/***/Documents/metrics/apps/toy/main.cpp:21:22: error: too few arguments to function call, expected 2, have 1  
cobalt::promise<int> cpu_intense_work(  
                     ^~~~~~~~~~~~~~~~  
/Users/***/***/include/boost/cobalt/this_coro.hpp:349:15: note: 'operator delete' declared here  
  static void operator delete(void * raw, const std::size_t size) noexcept  
              ^  
/Users/***/Documents/metrics/apps/toy/main.cpp:29:20: error: too few arguments to function call, expected 2, have 1  
cobalt::task<void> work(int min_a, int max_a, int b)  
                   ^~~~  
/Users/***/***/include/boost/cobalt/this_coro.hpp:349:15: note: 'operator delete' declared here  
  static void operator delete(void * raw, const std::size_t size) noexcept  
              ^  
In file included from /Users/***/Documents/metrics/apps/toy/main.cpp:8:  
In file included from /Users/***/***/include/boost/cobalt.hpp:25:  
In file included from /Users/***/***/include/boost/cobalt/race.hpp:12:  
/Users/***/***/include/boost/cobalt/detail/race.hpp:506:25: error: too few arguments to function call, expected 2, have 1  
    static detail::fork await_impl(awaitable & this_, std::size_t idx)  
                        ^~~~~~~~~~  
/Users/***/***/include/boost/cobalt/detail/race.hpp:608:21: note: in instantiation of member function 'boost::cobalt::detail::race_ranged_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned int, 48271, 0, 2147483647> &, std::list<boost::cobalt::promise<void>> &>::awaitable::await_impl' requested here  
      last_forked = await_impl(*this, reorder.front());  
                    ^  
/Users/***/***/include/boost/cobalt/detail/wait_group.hpp:38:23: note: in instantiation of member function 'boost::cobalt::detail::race_ranged_impl<boost::asio::cancellation_type::all, std::linear_congruential_engine<unsigned int, 48271, 0, 2147483647> &, std::list<boost::cobalt::promise<void>> &>::awaitable::await_ready' requested here  
        return impl_->await_ready();  
                      ^  
/Users/***/***/include/boost/cobalt/detail/fork.hpp:113:10: note: 'operator delete' declared here  
    void operator delete(void *, const std::size_t) noexcept {}  
         ^  
In file included from /Users/***/Documents/metrics/apps/toy/main.cpp:8:  
In file included from /Users/***/***/include/boost/cobalt.hpp:17:  
In file included from /Users/***/***/include/boost/cobalt/gather.hpp:12:  
/Users/***/***/include/boost/cobalt/detail/gather.hpp:303:25: error: too few arguments to function call, expected 2, have 1  
    static detail::fork await_impl(awaitable & this_, std::size_t idx)  
                        ^~~~~~~~~~  
/Users/***/***/include/boost/cobalt/detail/gather.hpp:344:23: note: in instantiation of member function 'boost::cobalt::detail::gather_ranged_impl<std::list<boost::cobalt::promise<void>> &>::awaitable::await_impl' requested here  
        last_forked = await_impl(*this, last_index++);  
                      ^  
/Users/***/***/include/boost/cobalt/detail/wait_group.hpp:90:23: note: in instantiation of member function 'boost::cobalt::detail::gather_ranged_impl<std::list<boost::cobalt::promise<void>> &>::awaitable::await_ready' requested here  
        return impl_->await_ready();  
                      ^  
/Users/***/***/include/boost/cobalt/detail/fork.hpp:113:10: note: 'operator delete' declared here  
    void operator delete(void *, const std::size_t) noexcept {}  
         ^  
4 errors generated.  
ninja: build stopped: subcommand failed.  
```

---

## Comment 1

> Username: assafcohen  
> Created at: 2023-12-27 18:38:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/144#issuecomment-1870541639  

Got it to work by disabling pmr
