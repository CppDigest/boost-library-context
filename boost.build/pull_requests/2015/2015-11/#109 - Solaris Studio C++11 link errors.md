# #109 Solaris Studio C++11 link errors [Closed]

> Username: akumta  
> Created at: 2015-11-19 19:00:23 UTC  
> Updated at: 2021-10-02 21:19:19 UTC  
> Closed at: 2019-02-23 15:34:52 UTC  
> Url: https://github.com/boostorg/build/pull/109  

Several python tests fail with the following error message when compiling with Oracle Solaris Studio compilers with -std=[c++03,c++11], modes.  
  
 ImportError?: ld.so.1: isapython2.6: fatal: relocation error: file /export/home/boost_regression_develop/boost_sparc-S2_cpp11/results/boost/bin.v2/libs/python/test/injected.test/sun-next_cpp11/ release/threading-multi/injected_ext.so: symbol _ZTIv: referenced symbol not found  
  
These errors occur when any libraries that are linked into the application that has no C++11 runtime already linked in.  
  
When creating shared libraries, replacing '-G' with '-G -library=stdcpp,CrunG3'   seems to resolve the issue.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-12-14 07:10:59 UTC  
> Url: https://github.com/boostorg/build/pull/109#issuecomment-164364921  

Thanks for the patch! Looking at it, I wonder whether this should be considered a compiler bug? If passing -std=c++11 results in object files that fail to work without -library=stdcpp,GrunG3, then I would imagine the compiler or linker should automatically link to that newer library? Is this something you can check with compiler vendor, before we try to work around this issue?

---

## Comment 2

> Username: akumta  
> Created_at: 2015-12-14 19:40:31 UTC  
> Url: https://github.com/boostorg/build/pull/109#issuecomment-164537820  

Yes this is a known compiler issue and the patch here is a workaround for it until the compiler issue is   
fixed.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2019-02-23 15:34:52 UTC  
> Url: https://github.com/boostorg/build/pull/109#issuecomment-466662509  

Assuming this has been resolved by the vendor. If it hasn't reopen.

---
