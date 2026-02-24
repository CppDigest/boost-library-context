# #182 - stacktrace_from_exception - set_capture_stacktraces_at_throw or link with boost_stacktrace_from_exception [Closed]

> Username: meastp  
> Created at: 2024-09-11 06:36:16 UTC  
> Updated at: 2024-09-12 11:47:35 UTC  
> Closed at: 2024-09-12 07:40:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/182  

I'm trying to get this functionality to work when building boost-stacktrace with vcpkg.  
  
Is linking with `boost_stacktrace_from_exception` required if using `boost::stacktrace::this_thread::set_capture_stacktraces_at_throw()` or are they alternatives to achieve the same result?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 07:40:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/182#issuecomment-2345496600  

The linking is required. Here's a quote from the docs:  
  
> Link with boost_stacktrace_from_exception library (or just LD_PRELOAD it!) and call boost::stacktrace::stacktrace::from_current_exception() to get the trace:

---

## Comment 2

> Username: meastp  
> Created at: 2024-09-12 11:47:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/182#issuecomment-2346071313  

@apolukhin thanks, but how do I build boost_stacktrace_from_exception using CMake? Are there any options to enable it when building boost_stacktrace_backtrace?
