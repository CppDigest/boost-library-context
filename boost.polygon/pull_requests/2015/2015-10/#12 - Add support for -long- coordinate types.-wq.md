# #12 Add support for "long" coordinate types.:wq [Merged]

> Username: plopresti  
> Created at: 2015-10-06 16:34:25 UTC  
> Updated at: 2015-10-08 20:52:55 UTC  
> Merged at: 2015-10-08 19:21:19 UTC  
> Closed at: 2015-10-08 19:21:19 UTC  
> Url: https://github.com/boostorg/polygon/pull/12  

Hi. I discovered that the following code does not compile:  
# include &lt;boost/polygon/isotropy.hpp>  
  
...  
typedef boost::polygon::coordinate_traits&lt;long>::area_type A;  
  
I found this because I really want my coordinates to be 64 bits exactly, so I am using "int64_t", which on x86_64 is a typedef for "long". (On 32-bit it is a typedef for "long long".)  
  
I believe this pull request covers the vast majority of cases without breaking anything, but I have only actually tested on 32-bit and 64-bit x86.

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2015-10-08 19:21:16 UTC  
> Url: https://github.com/boostorg/polygon/pull/12#issuecomment-146661534  

The patch seems valid to me. I will also check regular Boost build for any regressions.

---
