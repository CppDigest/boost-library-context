# #66 - Please explain how to detect this library with cmake [Closed]

> Username: mquinson  
> Created at: 2018-10-28 17:26:20 UTC  
> Updated at: 2018-10-30 21:49:33 UTC  
> Closed at: 2018-10-30 16:47:07 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/66  

Hello,  
  
detecting this library with cmake is a bit challenging and could deserve some words in the documentation. This is due to two facts.  
  
First, stacktrace is not a library on its own, but it proposes several libraries. So the line in CMakeFiles.txt should not read `find_package(Boost COMPONENTS stacktrace)` but for example `find_package(Boost COMPONENTS stacktrace_backtrace)` This should really be said in the documentation of this library, IMHO.  
  
The second problem is that modern cmake don't know about this library. See [bug 17297](https://gitlab.kitware.com/cmake/cmake/issues/17297). What is missing is the header files associated with each sublibraries. On my Debian system, I need to add the following lines before my call to `find_ package(Boost ....)`  
  
```  
set(_Boost_STACKTRACE_BASIC_HEADERS     "boost/stacktrace.hpp")  
set(_Boost_STACKTRACE_BACKTRACE_HEADERS "boost/stacktrace.hpp")  
set(_Boost_STACKTRACE_ADDR2LINE_HEADERS "boost/stacktrace.hpp")  
set(_Boost_STACKTRACE_NOOP_HEADERS      "boost/stacktrace.hpp")  
```  
  
These values are not perfect, and we should use a header file that is only available when this sublibrary is available, but I don't know boost enough... Do you have a better list to be included in cmake, maybe?

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-10-28 18:29:11 UTC  
> Updated at: 2018-10-28 18:31:09 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/66#issuecomment-433729516  

Boost does not have a CMake integration right now. We are working to change that fact, but it would not happen in this year.  
  
If you wish to do the detection on your own, then detect that header `boost/stacktrace.hpp` is available and make separate targets for each of the stacktrce libraries from here:  http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html . Do not forget to add a public macro definition `BOOST_STACKTRACE_LINK` to each of those targets.  
  
Make a target boost_stacktrace that chooses the best available library. Order of usefulness (from the most   
 usefull one): boost_stacktrace_windbg_cached, boost_stacktrace_windbg, boost_stacktrace_backtrace, [boost_stacktrace_addr2line,] boost_stacktrace_basic, boost_stacktrace_noop.  
  
Done. Now users could use target boost_stacktrace if they need some stacktracing, target boost_stacktrace_noop if they do not need one. Advanced users could manually choose stacktrace libraries. Changing a target would not cause project recompilation.

---

## Comment 2

> Username: mquinson  
> Created at: 2018-10-30 21:49:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/66#issuecomment-434482743  

Hello,  
  
it was not really a question, but a request for documentation. But you decide.  
  
Thanks for the library anyway.  
Mt
