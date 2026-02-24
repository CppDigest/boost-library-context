# #419 - Conversion warnings in VS2017 [Closed]

> Username: MikeGitb  
> Created at: 2017-08-26 05:18:31 UTC  
> Updated at: 2017-09-06 10:08:25 UTC  
> Closed at: 2017-09-06 10:08:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/419  

**TL;DR**:  
Visual studio complains about "possible loss of data" in some internal functions related to `region_id`, because variables of type `signed_size_type` are assigned to variables of type int. In particular, the internal representation seems to be `signed_size_type`, but the interface exposes it as `int`  
  
**Explanation**  
  
When compiling the cinder library (https://github.com/cinder/Cinder) with VS2017 - 15.3.2 and the latest boost version provided by vcpkg (1.65), I get quite a few conversion warnings from boost/geometry, with most of them being related to `region_id`.    
It seems that the `region_id` member variables (I found at least two classes with that member so far) are of type `signed_size_type`, wheras local variables and function parameters/return types are usually just ints (E.g. see here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/aggregate_operations.hpp#L148-L165)  
  
I can make a PR, that silences those particular warnings, however,  
- I don't know if the prefered solution would be to  either  
   - use static_casts (just tell the compiler to shut up)  
   - change the internal representation to `int`  
   - changethe interface representation to `signed_size_type` (which would probably have "ripple effects"   
- I'm not sure if that PR would be complete (the same problem might be in other functions that don't get instantiated when compiling cinder)  
- I've never contributed anything to boost, so I don't know how to properly test the PR or if I've to prepare it in a particular manner.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2017-08-26 08:33:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/419#issuecomment-325102891  

Thanks for the report. Sorry about this, I added region_id recently and was apparently not careful enough. I will fix this.

---

## Comment 2

> Username: MikeGitb  
> Created at: 2017-08-31 02:57:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/419#issuecomment-326175592  

No worries - thanks for the quick response. One of those days I'll have a look at how to write and test patches for boost myself.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2017-09-06 10:08:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/419#issuecomment-327438662  

I fixed it today and it is merged to develop. Thanks again.
