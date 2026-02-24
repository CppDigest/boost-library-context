# #112 Implement copy_reference and copy_cv_ref [Merged]

> Username: glenfe  
> Created at: 2019-02-22 21:28:57 UTC  
> Updated at: 2019-02-26 20:27:10 UTC  
> Merged at: 2019-02-26 20:16:43 UTC  
> Closed at: 2019-02-26 20:16:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112  



---

## Comment 1

> Username: glenfe  
> Created_at: 2019-02-23 21:01:38 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112#issuecomment-466694521  

Peter wants one of these (copy_cv_ref) for Variant2.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-02-26 09:26:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112#issuecomment-467363297  

These look fine: my only comment is that they are inconsistently named, do we change to `copy_cv_reference` or to `copy_ref`?  Any precedent?

---

## Comment 3

> Username: glenfe  
> Created_at: 2019-02-26 11:52:37 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112#issuecomment-467409351  

Affirmative: remove_reference, remove_cv_ref.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-02-26 18:49:08 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112#issuecomment-467563438  

So leave the names as they are then?

---

## Comment 5

> Username: glenfe  
> Created_at: 2019-02-26 20:00:35 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112#issuecomment-467590583  

Yes. :)

---

## Comment 6

> Username: glenfe  
> Created_at: 2019-02-26 20:27:10 UTC  
> Url: https://github.com/boostorg/type_traits/pull/112#issuecomment-467600053  

Thanks John.

---
