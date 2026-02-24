# #72 - Baffled by quick_exit_test [Closed]

> Username: eldiener  
> Created at: 2020-03-23 03:02:25 UTC  
> Updated at: 2020-11-18 23:53:50 UTC  
> Closed at: 2020-11-18 23:53:50 UTC  
> Url: https://github.com/boostorg/core/issues/72  

In the quick exit test it looks like the function quick_exit should only exist globally for CYGWIN less than C++11. Testing the Embarcadero toolset with the 64-bit clang-based C++ compiler bcc64 the compiler issues the error:  
  
..\..\..\boost/core/quick_exit.hpp:52:7: error: no type named 'quick_exit' in the global namespace  
  
This seems correct to me since in cppreference at https://en.cppreference.com/w/cpp/utility/program/quick_exit it shows the quick_exit function in the std:: namespace when including cstdlib. Did this change and what am I missing ?

---

## Comment 1

> Username: pdimov  
> Created at: 2020-03-23 03:17:32 UTC  
> Url: https://github.com/boostorg/core/issues/72#issuecomment-602363450  

I no longer remember why I had to switch from `std::quick_exit` to `::quick_exit` in https://github.com/boostorg/core/commit/d56c31d6885f1fa1b0b8903b137aa0b82e2acaf4. There must have been a reason.  
  
It's correct in principle that `<cstdlib>` is only required to declare `std::quick_exit` and that for `::quick_exit` one needs to use `<stdlib.h>`, but not many standard libraries enforce that.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-03-23 13:23:11 UTC  
> Url: https://github.com/boostorg/core/issues/72#issuecomment-602585847  

Here are the results of changing the `::quick_exit` call to `std::quick_exit`:  
  
https://travis-ci.org/github/boostorg/core/builds/665690041  
https://ci.appveyor.com/project/pdimov/core/builds/31642742  
  
The Cygwin failures are apparently because I declare `quick_exit` manually in the global namespace in C++03 mode, but everything Linux also fails.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-03-23 18:53:16 UTC  
> Url: https://github.com/boostorg/core/issues/72#issuecomment-602791090  

In the C++11 and C++14 standard documents it shows that quick_exit is not in the std namespace. But in the C++17 standard document it shows that quick_exit is in the std namespace.

---

## Comment 4

> Username: eldiener  
> Created at: 2020-03-23 19:02:07 UTC  
> Url: https://github.com/boostorg/core/issues/72#issuecomment-602795702  

All the exit functions are in the std namespace in C++17 where in C++11/C++14 they are in the global namespace.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-03-23 20:00:12 UTC  
> Url: https://github.com/boostorg/core/issues/72#issuecomment-602824667  

`<cstdlib>` declares it in `std`, while `<stdlib.h>` declares it in the global namespace. I changed `quick_exit.hpp` to include `<stdlib.h>` as it only worked in this case anyway (all the functions is uses are declared there.)  
  
https://en.cppreference.com/w/c/program/quick_exit
