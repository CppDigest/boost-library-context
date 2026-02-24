# #27 __visibility__ in forward decl [Closed]

> Username: aminiussi  
> Created at: 2015-09-21 16:56:27 UTC  
> Updated at: 2015-10-06 15:08:25 UTC  
> Closed at: 2015-10-06 15:08:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/27  

The visibility spec  does not seems to be relevant on forward class decalarations and it trigger a lot of spurious warning with intel icpc in linux.

---

## Comment 1

> Username: robertramey  
> Created_at: 2015-09-29 20:56:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/27#issuecomment-144189016  

I believe that all visibility issues have been addressed.  Please try again  
  
> On Sep 21, 2015, at 9:56 AM, Alain Miniussi notifications@github.com wrote:  
>   
> The visibility spec does not seems to be relevant on forward class decalarations and it trigger a lot of spurious warning with intel icpc in linux.  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/serialization/pull/27 https://github.com/boostorg/serialization/pull/27  
> Commit Summary  
>   
> The visibility spec does not seems to be relevant on forward class decalarations and it trigger a lot of spurious warning with intel icpc in linux.  
> File Changes  
>   
> M include/boost/archive/detail/interface_oarchive.hpp https://github.com/boostorg/serialization/pull/27/files#diff-0 (2)  
> M include/boost/archive/polymorphic_iarchive.hpp https://github.com/boostorg/serialization/pull/27/files#diff-1 (3)  
> M include/boost/archive/polymorphic_oarchive.hpp https://github.com/boostorg/serialization/pull/27/files#diff-2 (4)  
> Patch Links:  
>   
> https://github.com/boostorg/serialization/pull/27.patch https://github.com/boostorg/serialization/pull/27.patch  
> https://github.com/boostorg/serialization/pull/27.diff https://github.com/boostorg/serialization/pull/27.diff  
> —  
> Reply to this email directly or view it on GitHub https://github.com/boostorg/serialization/pull/27.  
  
Robert Ramey  
Robert Ramey Software Development  
(805)569-3793  
ramey@rrsd.com  
www.rrsd.com

---

## Comment 2

> Username: aminiussi  
> Created_at: 2015-10-06 15:08:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/27#issuecomment-145888036  

Seems ok.  
  
Thanks

---
