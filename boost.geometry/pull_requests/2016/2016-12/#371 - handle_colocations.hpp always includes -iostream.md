# #371 handle_colocations.hpp always includes <iostream> [Closed]

> Username: nre-ableton  
> Created at: 2016-12-22 12:50:47 UTC  
> Updated at: 2017-01-03 13:35:58 UTC  
> Closed at: 2017-01-03 13:35:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/371  

This file contains a few stray `std::cout` statements which do not seem to be related to `BOOST_GEOMETRY_DEBUG_IDENTIFIER`, and hence cause a compiler error if that symbol is undefined.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-01-03 13:17:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/371#issuecomment-270112052  

The library shouldn't depend on iostream. All occurances of `cout` statements are for debugging. In `handle_colocations.hpp` they are all guarded by `BOOST_GEOMETRY_DEBUG_HANDLE_COLOCATIONS`. Or am I missing something? Where have you seen the stray ones?

---

## Comment 2

> Username: nre-ableton  
> Created_at: 2017-01-03 13:35:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/371#issuecomment-270115221  

My bad, it looks like this was fixed in https://github.com/boostorg/geometry/commit/7fc45bef3febd449a38736a198809e9574c5efa3#diff-4461eb3f155f150363a79d411e287c53, which was not present in boost 1.62. I see now that boost 1.63 has been released, and it contains that fix. Sorry for the noise!

---
