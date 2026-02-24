# #801 Corrected paths for cubic and quartic roots headers in doc [Merged]

> Username: RL-S  
> Created at: 2022-07-15 15:28:08 UTC  
> Updated at: 2022-07-15 17:12:17 UTC  
> Merged at: 2022-07-15 17:12:17 UTC  
> Closed at: 2022-07-15 17:12:17 UTC  
> Url: https://github.com/boostorg/math/pull/801  

The current documentation lists `cubic_roots.hpp` and `quartic_roots.hpp` as located in `boost/math/`**`roots/`**, however, they are now both located in `boost/math/`**`tools`**.  
  
It appears that these two doc files are the only ones affected. Checked with `grep -rn "roots/" . | grep -v "doc/roots"` in the `libs/math/doc/roots` directory.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-07-15 17:12:11 UTC  
> Url: https://github.com/boostorg/math/pull/801#issuecomment-1185740501  

Yup, that looks right to me, no need for a CI run on a small doc change so merging...

---
