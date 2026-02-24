# #276 Disable use of int128 [Merged]

> Username: mkaravel  
> Created at: 2015-03-30 14:46:17 UTC  
> Updated at: 2015-03-31 11:33:31 UTC  
> Merged at: 2015-03-30 15:03:29 UTC  
> Closed at: 2015-03-30 15:03:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/276  

This PR disables (by default) the use of `boost::int128_type` and `boost::uint128_type`. The use of these types can be enabled by defining the macro `BOOST_GEOMETRY_ENABLE_INT128`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-30 16:26:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/276#issuecomment-87742107  

Thanks - just merged to master

---
