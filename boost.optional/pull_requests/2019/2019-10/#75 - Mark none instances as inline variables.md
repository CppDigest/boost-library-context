# #75 Mark none instances as inline variables [Closed]

> Username: Lastique  
> Created at: 2019-10-29 18:24:04 UTC  
> Updated at: 2019-12-19 20:10:51 UTC  
> Closed at: 2019-12-19 20:10:22 UTC  
> Url: https://github.com/boostorg/optional/pull/75  

This should avoid duplicating `none` instances in all translation units.  
  
Closes https://github.com/boostorg/optional/issues/33.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2019-10-31 17:19:15 UTC  
> Url: https://github.com/boostorg/optional/pull/75#issuecomment-548480578  

Thanks for the PR. I can see that `BOOST_INLINE_VARIABLE` is only defined in branch `develop`. To be on the safe side I'll wait until it is merged to `master`.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-12-19 20:10:22 UTC  
> Url: https://github.com/boostorg/optional/pull/75#issuecomment-567644274  

Closed by https://github.com/boostorg/optional/commit/9ed20cb0854120f76d4ccc0f9821c38553574f7e.

---
