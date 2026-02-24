# #386 Minor fix for gcc-4.4 with no rtti. [Closed]

> Username: jzmaddock  
> Created at: 2021-07-04 17:24:21 UTC  
> Updated at: 2022-11-20 17:45:43 UTC  
> Closed at: 2021-07-04 18:10:38 UTC  
> Url: https://github.com/boostorg/config/pull/386  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-07-04 18:10:38 UTC  
> Url: https://github.com/boostorg/config/pull/386#issuecomment-873635655  

On second thoughts, this is unfixable - apparently you cannot include <memory> in C++0x mode with -fno-rtti.

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-07-04 18:42:49 UTC  
> Url: https://github.com/boostorg/config/pull/386#issuecomment-873640009  

It's fixable in the .drone.star file by removing 0x from g++ 4.4. :-)

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-07-05 18:13:01 UTC  
> Url: https://github.com/boostorg/config/pull/386#issuecomment-874267312  

> It's fixable in the .drone.star file by removing 0x from g++ 4.4. :-)  
  
Yes, I know, but it's really only the one test that's effected.  When I get a chance I'll disable that one test.

---
