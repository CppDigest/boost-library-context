# #304 Replace use of num_points by is_empty [Merged]

> Username: mkaravel  
> Created at: 2015-06-03 14:22:35 UTC  
> Updated at: 2015-06-03 16:50:32 UTC  
> Merged at: 2015-06-03 14:33:28 UTC  
> Closed at: 2015-06-03 14:33:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/304  

This PR replaces instances of `bg::num_points() == 0` by `bg::is_empty()` and instances of `bg::num_points() > 0` by `! bg::is_empty()`.  
  
This is done both in the `include` and `test` directories.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-06-03 14:26:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/304#issuecomment-108452711  

Thnks I'm OK with merging

---
