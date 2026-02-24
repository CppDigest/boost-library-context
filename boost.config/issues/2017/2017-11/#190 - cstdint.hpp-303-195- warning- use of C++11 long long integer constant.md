# #190 - cstdint.hpp:303:195: warning: use of C++11 long long integer constant [Closed]

> Username: jeking3  
> Created at: 2017-11-06 12:47:16 UTC  
> Updated at: 2018-08-04 18:32:41 UTC  
> Closed at: 2018-08-04 18:32:41 UTC  
> Url: https://github.com/boostorg/config/issues/190  

While building Boost.Uuid through Appveyor for cygwin with `cxxflags=-std=c++03`, I am seeing this warning over 1,500 times:  
```  
./boost/cstdint.hpp:303:195: warning: use of C++11 long long integer constant [-Wlong-long]  
 #    elif (defined(ULLONG_MAX) && ULLONG_MAX == 18446744073709551615ULL) || (defined(ULONG_LONG_MAX) && ULONG_LONG_MAX == 18446744073709551615ULL) || (defined(ULONGLONG_MAX) && ULONGLONG_MAX == 18446744073709551615ULL)  
                                                                                                                                                                                                   ^  
```  
  
See build job:  
https://ci.appveyor.com/project/jeking3/uuid/build/1.0.114-develop/job/ws6sqg5vvsc20j1b

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-12-09 18:54:04 UTC  
> Url: https://github.com/boostorg/config/issues/190#issuecomment-350497817  

At first glance I don't see an easy fix - any ideas?

---

## Comment 2

> Username: lakshayg  
> Created at: 2018-02-19 18:02:03 UTC  
> Url: https://github.com/boostorg/config/issues/190#issuecomment-366766536  

I am unable to reproduce this issue locally but found this on SO: https://stackoverflow.com/a/33646357/3033441  
  
  
  
> It seems that the C++11 warning when using the ll suffix may be a gcc bug.  
>   
> A workaround (inspired by @nate-eldredge's comment) is to avoid using the literal and have it produced at compile time with constexpr:  
> ```  
> int64_t constexpr const trillion = int64_t(1'000'000) * int64_t(1'000'000);  
> if(nanoseconds < trillion) ...  
> ```

---

## Comment 3

> Username: Lastique  
> Created at: 2018-03-16 09:46:34 UTC  
> Url: https://github.com/boostorg/config/issues/190#issuecomment-373658571  

Maybe try silencing the warning with a `#pragma`?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-03-16 18:12:37 UTC  
> Url: https://github.com/boostorg/config/issues/190#issuecomment-373799947  

Yep, there is actually a #pragma system_header in there already, but I've moved it to catch more cases.  
  
BTW I'm unable to reproduce the original issue, even with -Wall -pedantic.  
  
See https://github.com/boostorg/config/commit/dca252c0870361a0ab52b247eeaa450a26e4f5dd
