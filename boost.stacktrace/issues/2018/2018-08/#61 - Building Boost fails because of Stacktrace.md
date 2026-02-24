# #61 - Building Boost fails because of Stacktrace [Closed]

> Username: pdimov  
> Created at: 2018-08-28 22:00:20 UTC  
> Updated at: 2018-08-29 10:26:40 UTC  
> Closed at: 2018-08-29 09:37:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/61  

Superproject CI builds have been failing for a while with  
  
```  
In file included from ./boost/stacktrace/detail/frame_unwind.ipp:25:0,  
                 from libs/stacktrace/build/../src/backtrace.cpp:15:  
./boost/stacktrace/detail/libbacktrace_impls.hpp: In member function ‘void boost::stacktrace::detail::to_string_using_backtrace::prepare_function_name(const void*)’:  
./boost/stacktrace/detail/libbacktrace_impls.hpp:108:13: error: invalid conversion from ‘void (*)(void*, uintptr_t, const char*, uintptr_t, uintptr_t) {aka void (*)(void*, long unsigned int, const char*, long unsigned int, long unsigned int)}’ to ‘backtrace_syminfo_callback {aka void (*)(void*, long unsigned int, const char*, long unsigned int)}’ [-fpermissive]  
             );  
             ^  
./boost/stacktrace/detail/libbacktrace_impls.hpp: In function ‘std::string boost::stacktrace::detail::name_impl(const void*)’:  
./boost/stacktrace/detail/libbacktrace_impls.hpp:155:9: error: invalid conversion from ‘void (*)(void*, uintptr_t, const char*, uintptr_t, uintptr_t) {aka void (*)(void*, long unsigned int, const char*, long unsigned int, long unsigned int)}’ to ‘backtrace_syminfo_callback {aka void (*)(void*, long unsigned int, const char*, long unsigned int)}’ [-fpermissive]  
         );  
         ^  
```  
  
see f.ex. https://travis-ci.org/boostorg/boost/jobs/421389489

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-08-29 10:26:39 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/61#issuecomment-416904399  

Now build is OK https://travis-ci.org/boostorg/boost/builds/421982313
