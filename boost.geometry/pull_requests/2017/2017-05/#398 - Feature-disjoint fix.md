# #398 Feature/disjoint fix [Merged]

> Username: vissarion  
> Created at: 2017-05-05 13:09:20 UTC  
> Updated at: 2017-07-06 12:49:06 UTC  
> Merged at: 2017-05-23 11:23:59 UTC  
> Closed at: 2017-05-23 11:23:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/398  

Fixing disjoint algorithm for geographic computation. A main case in disjoint algorithm relies in computing the vertex longitude of a segment. Thus, tests are also added for vertex longitude computation.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-05-10 14:30:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/398#pullrequestreview-37313674  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 156 |+         std::cout << "vertex=" <<  vertex_lon * math::r2d<CT>()
 157 |+                       << " , " <<  vertex_lat * math::r2d<CT>()
 158 |+                      << std::endl;
```

> Username: awulkiew  
> Created_at: 2017-05-10 14:04:53 UTC  
> Updated_at: 2017-05-11 08:42:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/398#discussion_r115749012  

This should either be removed or disabled by default and enabled with `#ifdef`.

> Username: vissarion  
> Created_at: 2017-05-11 08:18:37 UTC  
> Updated_at: 2017-05-11 08:42:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/398#discussion_r115929784  

This was removed in commit b7d5f4b84951df8147a570f32b0b5066da419261


📁 include/boost/geometry/formulas/vertex_longitude.hpp

```diff
  90 |         CT const f = flattening<CT>(spheroid);
  91 |         CT const C1 = CT(1);
  92 |+         CT const C4 = CT(4);
```

> Username: awulkiew  
> Created_at: 2017-05-10 14:10:05 UTC  
> Updated_at: 2017-05-11 08:42:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/398#discussion_r115750332  

In general variable names should be lower-case names. In other places of the library constants such as these are already lowe-case. In this file below `c0`, `c1` and `c3` are used for constants. So I think that they should be named consistently in this file at least, but it'd probably be good idea to use names consistently across the whole library.

---

📁 include/boost/geometry/formulas/vertex_longitude.hpp

```diff
 153 |         CT const C31 = (c1 - c1 * n) * eps + c2 * eps2;
 154 |         CT const C32 = c3 * eps2;
```

> Username: awulkiew  
> Created_at: 2017-05-10 14:18:12 UTC  
> Updated_at: 2017-05-11 08:42:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/398#discussion_r115752615  

Also here there are constants `C31` and `C32` which are conceptually somethign different, they are not representations of numbers `31` and `32`. So I think they should be named differently than other constants. So upper-case `C` is probably ok here since it makes the name different. The name could also be something more descriptive (like `sin2_sig2`).

---

📁 include/boost/geometry/formulas/vertex_longitude.hpp

```diff
 184 |+     (
 185 |+             false, NOT_IMPLEMENTED_FOR_THIS_COORDINATE_SYSTEM, (types<CS_Tag>)
 186 |+             );
```

> Username: awulkiew  
> Created_at: 2017-05-10 14:19:16 UTC  
> Updated_at: 2017-05-11 08:42:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/398#discussion_r115752937  

There are some changes in indentation which seems to be mistakes or something automatic, also above.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-05-10 14:34:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/398#issuecomment-300501631  

I'm not sure why some my comments are marked as outdated because at least one of them still holds but anyway. I'm ok with merging after addressing the comments.

---
