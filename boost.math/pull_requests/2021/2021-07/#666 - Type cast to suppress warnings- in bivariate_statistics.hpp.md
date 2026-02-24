# #666 Type cast to suppress warnings: in bivariate_statistics.hpp [Closed]

> Username: kojiynet  
> Created at: 2021-07-20 11:29:39 UTC  
> Updated at: 2021-07-25 04:35:07 UTC  
> Closed at: 2021-07-25 04:35:07 UTC  
> Url: https://github.com/boostorg/math/pull/666  

There "i"s are size_t but "Real" will be usually floating point type. We may want to explicitly cast the type.  
I did not see any error or bug on these, but we might prefer these changes to suppress warnings a compiler will issues; at least MSVC will issue warnings on these.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-07-22 17:47:19 UTC  
> Url: https://github.com/boostorg/math/pull/666#issuecomment-885097676  

By incredibly bad luck, there is a merge conflict. Could you close and apply these changes on top of the develop? The merge is surprisingly ugly.

---

## Comment 2

> Username: kojiynet  
> Created_at: 2021-07-25 04:34:50 UTC  
> Url: https://github.com/boostorg/math/pull/666#issuecomment-886145402  

OK, I will try!!

---
