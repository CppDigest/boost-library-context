# #74 Fixing several warnings given by pedantic g++-4.8.2 [Merged]

> Username: ddemidov  
> Created at: 2014-03-22 18:44:24 UTC  
> Updated at: 2014-06-30 05:45:48 UTC  
> Merged at: 2014-03-24 04:23:21 UTC  
> Closed at: 2014-03-24 04:23:21 UTC  
> Url: https://github.com/boostorg/compute/pull/74  

This fixes several warnings (mostly unused local typedefs, and one unused parameter) given by g++-4.8.2 with `-pedantic` flag. The one with unused parameter is interesting, because it is in a copy constructor, and the [comment says](https://github.com/ddemidov/compute/blob/6c8f158c004238d261bab9b6494c60cb900f61da/include/boost/compute/wait_list.hpp#L35-L38) the parameter would be copied, but the constructor is empty.  
  
I've seen the warnings when compiling Boost.Compute wrapping code for VexCL, which only uses scan and sort algorithms, so there could be more.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-03-22 19:03:36 UTC  
> Url: https://github.com/boostorg/compute/pull/74#issuecomment-38360952  

[![Coverage Status](https://coveralls.io/builds/615759/badge)](https://coveralls.io/builds/615759)  
  
Coverage decreased (-92.27%) when pulling **d653df535dc3782f5840433865af0ac6a8e9480d on ddemidov:master** into **667aa9c200bc845855f20f5e23f5887359966d5f on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-03-24 04:23:22 UTC  
> Url: https://github.com/boostorg/compute/pull/74#issuecomment-38411423  

Looks good! Thanks!  
  
Yeah, that's a bug in the copy-constructor for `wait_list`. I'll fix it.

---
