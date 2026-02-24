# #1094 - [Compilation Error] Missing <boost/core/static_assert.hpp>... [Open]

> Username: nbooster  
> Created at: 2025-10-26 10:26:25 UTC  
> Updated at: 2025-10-30 14:11:21 UTC  
> Url: https://github.com/boostorg/boost/issues/1094  

Hello,  
  
I just tried to build my C++ project again today  
with this on my CMake:  
  
```cmake  
Include(FetchContent)  
  
set(FETCHCONTENT_QUIET TRUE)  
  
FetchContent_Declare(Boost  
  GIT_REPOSITORY https://github.com/boostorg/boost.git  
  GIT_TAG        master # Boost 1.89  
  OVERRIDE_FIND_PACKAGE  
  GIT_PROGRESS TRUE  
)  
  
FetchContent_MakeAvailable(Boost)  
```  
  
and I got this:  
  
```linux  
/boost-src/libs/url/include/boost/url/grammar/charset.hpp:15:10: fatal error: boost/core/static_assert.hpp: No such file or directory  
   15 | #include <boost/core/static_assert.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
compilation terminated.  
```  
  
Does this look familiar or is t's just me ?  
  
*If I switch the GIT_TAG from master to --> boost-1.89.0 everything builds ok...  
  
Thank You.

---

## Comment 1

> Username: oliverportcnc  
> Created at: 2025-10-30 13:46:20 UTC  
> Updated at: 2025-10-30 13:51:05 UTC  
> Url: https://github.com/boostorg/boost/issues/1094#issuecomment-3468104379  

Having the same issue. It looks like [this commit](https://github.com/boostorg/core/commit/67aaac82d80998adb2240ca93806a93ac50ec88c) broke the other non-core libs.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-10-30 14:02:55 UTC  
> Updated at: 2025-10-30 14:05:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1094#issuecomment-3468181754  

This is not accurate:  
  
`GIT_TAG        master # Boost 1.89`  
  
It might say:  
  
`GIT_TAG        boost-1.89.0 # Boost 1.89`  
  
or:   
  
`GIT_TAG        master # pre-release, changing, 1.90`

---

## Comment 3

> Username: nbooster  
> Created at: 2025-10-30 14:09:59 UTC  
> Updated at: 2025-10-30 14:11:21 UTC  
> Url: https://github.com/boostorg/boost/issues/1094#issuecomment-3468218847  

It was just a comment to indicate the current master version...  
Anyway, it looks like the master branch compiles with no errors now (I just tested again right now).  
Please confirm...  
You can close the issue if you like... (I hope it stays that way...)
