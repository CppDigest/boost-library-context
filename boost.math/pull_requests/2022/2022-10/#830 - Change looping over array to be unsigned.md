# #830 Change looping over array to be unsigned [Merged]

> Username: SiliconA-Z  
> Created at: 2022-10-04 15:02:20 UTC  
> Updated at: 2022-10-13 14:06:47 UTC  
> Merged at: 2022-10-13 14:06:47 UTC  
> Closed at: 2022-10-13 14:06:47 UTC  
> Url: https://github.com/boostorg/math/pull/830  

This way of writing the loops is better and does not invoke undefined behavior or implementation defined behavior since size_t is unsigned

---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-10-04 17:04:55 UTC  
> Url: https://github.com/boostorg/math/pull/830#issuecomment-1267302374  

@AtariDreams : Do you have an ASan log I could see? We should also probably add a unit test to ensure this doesn't happen again.

---

## Comment 2

> Username: SiliconA-Z  
> Created_at: 2022-10-11 15:55:31 UTC  
> Url: https://github.com/boostorg/math/pull/830#issuecomment-1274922401  

@NAThompson No, I just noticed it was undefined behavior.

---

## Review 3 [Changes requested]

> Username: mborland  
> Created_at: 2022-10-11 16:11:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/830#pullrequestreview-1137798922  

📁 include/boost/math/interpolators/catmull_rom.hpp

```diff
 113 |     auto tmp = m_pnts[num_pnts-1];
 114 |-     for (std::ptrdiff_t i = num_pnts-1; i >= 0; --i)
 114 |+     for (size_t i = num_pnts; i != 0; --i)
```

> Username: mborland  
> Created_at: 2022-10-11 16:10:47 UTC  
> Updated_at: 2022-10-11 16:11:19 UTC  
> Url: https://github.com/boostorg/math/pull/830#discussion_r992528883  

Can you change the type to `std::size_t` to match `num_pnts` or make it `auto`?

> Username: SiliconA-Z  
> Created_at: 2022-10-11 16:42:15 UTC  
> Url: https://github.com/boostorg/math/pull/830#discussion_r992559618  

Done


---

## Comment 4

> Username: SiliconA-Z  
> Created_at: 2022-10-11 16:48:04 UTC  
> Url: https://github.com/boostorg/math/pull/830#issuecomment-1274989643  

@mborland Ready!

---
