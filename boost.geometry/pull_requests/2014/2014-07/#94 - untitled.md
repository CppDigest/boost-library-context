# #94 [multi][algorithms][num_points] [Merged]

> Username: mkaravel  
> Created at: 2014-07-14 11:47:30 UTC  
> Updated at: 2014-07-14 18:14:46 UTC  
> Merged at: 2014-07-14 14:33:41 UTC  
> Closed at: 2014-07-14 14:33:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/94  

1. add include for std::size_t  
2. qualify with std:: an occurrence of unqualified size_t

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-07-14 14:32:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/94#issuecomment-48906849  

Good catch!  
`<cstddef>` is probably not needed since it's already included in `<boost/geometry/algorithms/num_points.hpp>`. Plus, the implementation for multi will be merged with the implementation for single eventually. But ok, better safe than sorry.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-07-14 18:14:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/94#issuecomment-48936170  

On 14/07/2014 05:33 μμ, Adam Wulkiewicz wrote:  
  
> Good catch!  
> |<cstddef>| is probably not needed since it's already included in   
> |<boost/geometry/algorithms/num_points.hpp>|. Plus, the implementation   
> for multi will be merged with the implementation for single   
> eventually. But ok, better safe than sorry.  
  
I agree. Not, really needed, but thought (as you point-out) that it is   
safer this way.  
- m.  
  
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/94#issuecomment-48906849.

---
