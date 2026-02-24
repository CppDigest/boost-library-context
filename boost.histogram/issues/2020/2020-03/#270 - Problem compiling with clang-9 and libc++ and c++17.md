# #270 - Problem compiling with clang-9 and libc++ and c++17 [Closed]

> Username: JonasToth  
> Created at: 2020-03-04 21:32:44 UTC  
> Updated at: 2020-03-09 11:03:21 UTC  
> Closed at: 2020-03-09 11:03:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/270  

Hi,  
  
I am using this great library in a project that compiles with C++17 but up to date toolchains.  
  
I believe the condition at  
https://github.com/boostorg/histogram/blob/a872c6e1c32e3c6000317b07aec7e416c8939c75/include/boost/histogram/detail/span.hpp#L11  
is not fully correct, because e.g. clang-9 fullfills all those conditions, and has the include for `<span>`, but libc++ does not enable this include for C++17.  
  
Adding a `__cpp_lib_span >= 201902` as condition does fix this issue. (see https://en.cppreference.com/w/User:D41D8CD98F/feature_testing_macros#C.2B.2B20)  
  
Thanks again for the great library!  
  
Best Regards, Jonas

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-03-05 09:10:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/270#issuecomment-595113436  

Thank you very much for reporting this. I also found similar issues with this, the condition is difficult to get right. If you could make a PR I would be very happy to merge it.

---

## Comment 2

> Username: JonasToth  
> Created at: 2020-03-09 11:03:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/270#issuecomment-596461632  

This is fixed, so closing the issue.
