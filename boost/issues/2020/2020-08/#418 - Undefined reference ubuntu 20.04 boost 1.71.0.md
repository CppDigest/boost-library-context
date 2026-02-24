# #418 - Undefined reference ubuntu 20.04 boost 1.71.0 [Open]

> Username: iAhtasham  
> Created at: 2020-08-04 07:27:00 UTC  
> Updated at: 2020-11-19 14:44:59 UTC  
> Url: https://github.com/boostorg/boost/issues/418  

Hello I am new to ubuntu and was trying to compile firestorm viewer for a week now and failed. No matter what I try always failed with same error  
undefined reference to `boost::re_detail_107200::cpp_regex_traits_implementation::transform_primary[abi:cxx11](char const*, char const*) const'....................  
I searched online they said I have to compile boostlib myself to make it work. but that doesn't make any sense . I tried with different gcc such as 4.9 / 6.4 / 8.3 / 9.1 but faced same issue with all of them  
Thank you...

---

## Comment 1

> Username: ayush0x00  
> Created at: 2020-08-05 12:42:12 UTC  
> Url: https://github.com/boostorg/boost/issues/418#issuecomment-669170462  

The boost library you are using depends on the boost_system library.  
Assuming you use gcc, try adding **-lboost_system** to your compiler command line in order to link against that library.

---

## Comment 2

> Username: andrewes  
> Created at: 2020-11-19 14:44:59 UTC  
> Url: https://github.com/boostorg/boost/issues/418#issuecomment-730421726  

I'm compiling boost 1.71.0 on Ubuntu 20.04 without a problem. I compile it with clang version 10.0.0-4ubuntu1, but that should not matter.
