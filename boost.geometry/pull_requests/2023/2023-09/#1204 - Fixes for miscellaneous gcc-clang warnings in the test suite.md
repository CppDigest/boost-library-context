# #1204 Fixes for miscellaneous gcc/clang warnings in the test suite. [Merged]

> Username: tinko92  
> Created at: 2023-09-30 11:09:52 UTC  
> Updated at: 2023-12-04 10:46:32 UTC  
> Merged at: 2023-12-01 13:10:07 UTC  
> Closed at: 2023-12-01 13:10:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204  

This fixes a couple of warnings to declutter the output of running `b2 test` with GCC 13 or Clang 16 (and possibly other versions which I do not have installed):  
* a missing template keyword warning with GCC (which looks like a compiler bug to me but the workaround only costs two brackets),  
* some warnings for comparing signed and unsigned integers,  
* missing parentheses warnings,  
* an unused parameter warning (just commented out because the lining that would have used it is also just commented out).  
  
The remaining warnings (with the compilers that I have) come from Boost.Polygon and Boost.Test.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2023-10-03 08:33:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1204#pullrequestreview-1654647301  

Thanks!

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2023-10-04 11:28:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1204#pullrequestreview-1657303144  

Thanks!

📁 include/boost/geometry/formulas/meridian_direct.hpp

```diff
  86 |             }
  87 |-             else if (result.lat2 < -half_pi &&
  87 |+             else if ((result.lat2 < -half_pi) &&
```

> Username: barendgehrels  
> Created_at: 2023-10-04 11:28:35 UTC  
> Updated_at: 2023-10-04 11:28:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1345640015  

Weird that it warns for this, and not for the rest. We never have brackets here, unless and/or are combined.  
But anyway - if it fixes a warning, it's fine.

> Username: tinko92  
> Created_at: 2023-10-04 12:07:17 UTC  
> Updated_at: 2023-10-04 12:07:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1345689041  

I couldn't really make sense of it and the warning is only shown with GCC IIRC. `lat2`, assuming I didn't miss an alternative definition of the result type is just not a templated field. My only guess (which I don't find convincing) is that it maybe misreads part of  
```  
else if (result.lat2 < -half_pi &&  
         result.lat2 >  -one_and_a_half_pi)  
  
```  
As `result.lat2</*boolean template argument*/> - one_and_a_half_pi`. But then the logic that decides whether to emit a warning here would have to be different from the logic that correctly interprets and compiles that code and I would not expect a bug like that in GCC and I have no experience debugging GCC.

> Username: vissarion  
> Created_at: 2023-10-04 12:58:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1345756646  

Are you getting the same warning with gcc 12?

> Username: tinko92  
> Created_at: 2023-10-04 13:11:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1345774239  

Yes, I get the warning with both gcc12 and gcc13.

> Username: tinko92  
> Created_at: 2023-10-04 13:24:30 UTC  
> Updated_at: 2023-10-04 13:24:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1345793852  

Changing the second line of the condition to `-one_and_a_half_pi < result.lat2` also makes the warning disappear.

> Username: barendgehrels  
> Created_at: 2023-10-04 20:07:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1346401999  

Interesting...

> Username: barendgehrels  
> Created_at: 2023-10-04 20:24:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1346418662  

Could it be that it somehow interprets `< -half_pi && result.lat2 >` as a template?  
Maybe you can try to reverse the conditions  
```  
(result.lat2 >  -one_and_a_half_pi   
 && result.lat2 < -half_pi)  
           
```

> Username: barendgehrels  
> Created_at: 2023-10-04 20:25:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1204#discussion_r1346419801  

or probably not, it was a warning. The reversal you suggested is also fine.


---
