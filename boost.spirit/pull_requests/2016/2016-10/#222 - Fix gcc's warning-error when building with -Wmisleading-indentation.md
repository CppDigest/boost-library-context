# #222 Fix gcc's warning/error when building with -Wmisleading-indentation [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2016-10-16 20:20:48 UTC  
> Updated at: 2017-06-22 22:20:14 UTC  
> Merged at: 2016-10-16 21:13:31 UTC  
> Closed at: 2016-10-16 21:13:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/222  

Hi,  
  
I am having troubles when including "boost/spirit/include/qi.hpp" with gcc 6 and -Wall -Werror turned on. gcc (wrongly) complains about misleading indentation in the macro SPIRIT_NUMERIC_INNER_LOOP. This is likely caused by the fact that we have extra indentation after each line so that the backlslashes are properly aligned, and the macro is eventually putting this on a single line. Anyway, an easy fix is to use braces around all block of code.  
  
Ok for the merge ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2017-06-22 22:20:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/222#issuecomment-310518636  

Hi,  
  
I have recently migrated to Boost 1.64 and somehow was expected this fix to be included, but it was not. It looks like the master branch wasn't update since October 2016, is this expected ? Do you plan to merge "develop" into "master" for Boost 1.65 ?  
  
Cheers,  
Romain

---
