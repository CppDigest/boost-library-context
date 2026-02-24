# #32 - Compilation under nvcc with c++20 fails due to std::source_location::current being non-constexpr [Closed]

> Username: ricrogz  
> Created at: 2023-06-21 20:34:17 UTC  
> Updated at: 2023-06-22 15:44:18 UTC  
> Closed at: 2023-06-22 15:44:18 UTC  
> Url: https://github.com/boostorg/assert/issues/32  

Compiling my code on Linux with release 12.1, V12.1.105 / gcc 11.3.0 fails with the following errors:  
  
```  
/build_deps/boost_1_81_0/include/boost/throw_exception.hpp(224): error: call to consteval function "std::source_location::current" did not produce a valid constant expression  
  template<class E> __attribute__ ((__noreturn__)) void throw_with_location( E && e, boost::source_location const & loc = ::boost::source_location(::std::source_location::current()) )  
                                                                                                                                                                                  ^  
/build_deps/gcc-11.3.0/include/c++/11.3.0/source_location(51): note #2703-D: cannot call non-constexpr function "__builtin_source_location" (declared implicitly)  
      current(const void* __p = __builtin_source_location()) noexcept  
                                ^  
```  
  
I have hit this with both boost 1.81.0 and 1.82.0. I haven't tried with other compilers (gcc versions or clang) or nvcc.  
  
I have been able to track this back to `boost/assert/source_location.hpp`, where a similar issue is mentioned for some versions of MSVC. I'm submitting a simple patch that works around the issue.
