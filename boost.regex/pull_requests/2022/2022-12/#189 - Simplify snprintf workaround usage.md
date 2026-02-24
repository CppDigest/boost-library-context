# #189 Simplify snprintf workaround usage. [Merged]

> Username: jzmaddock  
> Created at: 2022-12-13 18:49:23 UTC  
> Updated at: 2022-12-16 12:13:06 UTC  
> Merged at: 2022-12-15 11:43:05 UTC  
> Closed at: 2022-12-15 11:43:05 UTC  
> Url: https://github.com/boostorg/regex/pull/189  

Via boost/core/snprintf.hpp  
Remove some obsolete workarounds.  
Remove tabs in files.  
Supersedes https://github.com/boostorg/regex/pull/188.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-12-14 10:10:02 UTC  
> Url: https://github.com/boostorg/regex/pull/189#issuecomment-1350786817  

I can't test this right now, but from the looks of it you're missing a workaround for `swprintf`. On MSVC and MinGW it doesn't have the buffer length argument. Boost.Core has a workaround for this.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-12-14 13:10:48 UTC  
> Url: https://github.com/boostorg/regex/pull/189#issuecomment-1351329067  

I think, you can drop the check for `BOOST_NO_SWPRINTF` when Boost.Core is used.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-12-14 18:40:23 UTC  
> Url: https://github.com/boostorg/regex/pull/189#issuecomment-1351947939  

>I think, you can drop the check for BOOST_NO_SWPRINTF when Boost.Core is used.  
  
Done, it's redundant anyway, and at some point a conforming C++11 compiler/library will be a minimum requirement anyway.

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-12-16 12:13:05 UTC  
> Url: https://github.com/boostorg/regex/pull/189#issuecomment-1354672691  

Please, also merge this to master.

---
