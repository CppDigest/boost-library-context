# #958 [sectionalize] remove dependency on define [Merged]

> Username: barendgehrels  
> Created at: 2022-01-05 10:56:45 UTC  
> Updated at: 2022-01-12 09:19:32 UTC  
> Merged at: 2022-01-12 09:19:29 UTC  
> Closed at: 2022-01-12 09:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/958  

This PR removes the `#if defined(BOOST_GEOMETRY_USE_RESCALING)` condition from sectionalize, and makes the implementation shorter anyway.  
This is the last real dependency on the define `BOOST_GEOMETRY_USE_RESCALING` and `BOOST_GEOMETRY_NO_ROBUSTNESS`, which means we can change the default somewhere (hopefully very soon) after this PR is merged.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-01-05 10:57:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/958#pullrequestreview-844468934  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 760 |-         static const coord_t eps = std::is_same<coord_t, float>::value
 761 |-             ? coord_t(1e-6)
 762 |-             : coord_t(1e-12);
```

> Username: barendgehrels  
> Created_at: 2022-01-05 10:57:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/958#discussion_r778728008  

These values correspond with the new values


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2022-01-05 10:58:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/958#pullrequestreview-844470010  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 803 |-                                    section.bounding_box);
 804 |- #endif
 751 |+         static ct const eps = math::scaled_epsilon<ct>(1000);
```

> Username: barendgehrels  
> Created_at: 2022-01-05 10:58:21 UTC  
> Updated_at: 2022-01-05 10:58:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/958#discussion_r778728734  

This is similar to `numeric_limits<T>::epsilon() * 1000` and we could replace it if you prefer that.


---
