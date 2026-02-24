# #104 Fix gcc's warning/error when building with -Wmisleading-indentation [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2016-10-16 20:30:51 UTC  
> Updated at: 2016-11-01 08:57:18 UTC  
> Closed at: 2016-10-18 21:46:08 UTC  
> Url: https://github.com/boostorg/test/pull/104  

Hi,  
  
I am having troubles when including "boost/test/unit_test.hpp" with gcc 6 and -Wall -Werror turned on. gcc (wrongly) complains about misleading indentation in the macro DEFINE_FPV_COMPARISON. This is likely caused by the fact that we have extra indentation after each line so that the backlslashes are properly aligned, and the macro is eventually putting this on a single line. Anyway, an easy fix is to use braces around all block of code.  
  
Ok for the merge ?  
  
(Similar to https://github.com/boostorg/spirit/pull/222)  
  
Cheers,  
Romain

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-10-18 21:46:07 UTC  
> Url: https://github.com/boostorg/test/pull/104#issuecomment-254648948  

Thanks, this is a duplicate of #103 that is already merged to a feature/check branch.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2016-11-01 08:57:18 UTC  
> Url: https://github.com/boostorg/test/pull/104#issuecomment-257517491  

In develop

---
