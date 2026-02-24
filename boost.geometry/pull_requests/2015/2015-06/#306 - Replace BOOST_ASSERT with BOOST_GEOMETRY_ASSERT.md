# #306 Replace BOOST_ASSERT with BOOST_GEOMETRY_ASSERT [Merged]

> Username: awulkiew  
> Created at: 2015-06-08 11:50:14 UTC  
> Updated at: 2015-06-08 19:14:20 UTC  
> Merged at: 2015-06-08 19:14:15 UTC  
> Closed at: 2015-06-08 19:14:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/306  

This allows to implement user-defined assertion failure handling mechanism specifically for Boost.Geometry assertions, e.g. to replace it with exception throw and/or logging or custom terminate, etc. The implementation is similar to the one in Boost.Assert so interested users should be familiar with it.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-06-08 14:19:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/306#issuecomment-110010373  

I would be nice to have a unit test where a user-defined assertion handling is used.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-06-08 17:23:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/306#issuecomment-110082700  

@mkaravel I think that if a function definition was defined as inline function in the same file that includes Boost.Geometry headers then the function would be inlined. And AFAIU the forward declaration is here in order to generate an error message during the linking phase.  
  
We could of course always assume that the required functions were declared before the `core/asert.hpp` header was included however this could be confusing and less flexible. Since now the user could define the handler function in a separate unit.  
  
Another alternative would be to define the macros only if they were not defined before. But I don't consider it as being safe enough.  
  
Of course I'll create a test throwing exceptions.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-06-08 18:12:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/306#issuecomment-110095211  

Good idea this gives some extra flexibility.  
I'm OK with merging  
Thanks for the PR

---
