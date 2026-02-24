# #638 - expand_by_epsilon does not compile if BOOST_GEOMETRY_NO_ROBUSTNESS is defined [Closed]

> Username: barendgehrels  
> Created at: 2019-11-07 18:43:28 UTC  
> Updated at: 2019-11-07 22:58:06 UTC  
> Closed at: 2019-11-07 22:54:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/638  

See title, please make sure the code compiles with this define.  
  
Now I get:  
  
.../boost/geometry/algorithms/detail/sections/sectionalize.hpp:798:9: error: no type named 'expand_by_epsilon' in namespace 'boost::geometry::detail'; did you mean simply  
      'expand_by_epsilon'?  
        detail::expand_by_epsilon(box);  
        ^~~~~~~~~~~~~~~~~~~~~~~~~  
        expand_by_epsilon

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-11-07 19:56:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/638#issuecomment-551238309  

I didn't test it yet but the cause is most likely the missing `#include`.  
  
I can either remove this `#ifdef` :  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp#L61 and always include `<boost/geometry/algorithms/detail/expand_by_epsilon.hpp`  
or add `#ifdef` here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp#L788  
and only define these templates if `BOOST_GEOMETRY_USE_RESCALING` is defined.  
  
Option 1 is probably simpler and safer.  
But which one do you prefer?

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-11-07 20:05:36 UTC  
> Updated at: 2019-11-07 20:08:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/638#issuecomment-551241783  

@barendgehrels which test are you trying to compile? The one I tried (intersection) is fine but it probably depends on the `#includes`.  
  
Edit: and maybe on compiler or rather the state of incremental build.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-11-07 20:32:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/638#issuecomment-551251286  

Hi @awulkiew  thanks for picking it up so quickly!  
  
I'm trying union.cpp, with that define. But I must add that I'm using my local branch (after rebasing it on latest develop, before it was no problem).  
  
If Option 1 is simpler and safer, please go ahead with that one.

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-11-07 22:53:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/638#issuecomment-551303781  

Fixed: https://github.com/boostorg/geometry/commit/074122d6195fd00dc028c34b3752ad4076562398

---

## Comment 5

> Username: barendgehrels  
> Created at: 2019-11-07 22:58:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/638#issuecomment-551305037  

right, thank you!
