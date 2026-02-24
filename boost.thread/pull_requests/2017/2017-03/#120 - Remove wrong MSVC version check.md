# #120 Remove wrong MSVC version check [Merged]

> Username: MarcelRaad  
> Created at: 2017-03-24 21:29:48 UTC  
> Updated at: 2017-04-21 04:36:34 UTC  
> Merged at: 2017-04-21 00:10:19 UTC  
> Closed at: 2017-04-21 00:10:19 UTC  
> Url: https://github.com/boostorg/thread/pull/120  

There is no compiler with `(_MSC_VER > 2000)` and taking the legacy codepath would probably break compilation again when there is. As `(_MSC_VER < 1300)` (that would be MSVC up to 6.0 from 1998) is not supported by Boost anymore anyway and cannot compile Boost.Thread, this codepath can just be removed completely.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2017-04-21 04:36:34 UTC  
> Url: https://github.com/boostorg/thread/pull/120#issuecomment-296059358  

Thanks for merging!

---
