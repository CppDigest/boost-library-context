# #34 Add remove_cv_ref [Merged]

> Username: pdimov  
> Created at: 2017-05-20 14:16:24 UTC  
> Updated at: 2017-05-21 13:43:21 UTC  
> Merged at: 2017-05-21 09:52:30 UTC  
> Closed at: 2017-05-21 09:52:30 UTC  
> Url: https://github.com/boostorg/type_traits/pull/34  

This adds the new transformation trait remove_cv_ref, with the obvious semantics.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-05-21 09:53:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/34#issuecomment-302926535  

Thanks, should we have the obvious make_const_reference as well?

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-05-21 12:07:02 UTC  
> Url: https://github.com/boostorg/type_traits/pull/34#issuecomment-302932655  

Not sure about that. I haven't come across a need for it, and the semantics aren't as obvious, because `add_const_reference<int&>` could legitimately be construed to mean either `int&` or `int const&`.  
  
Realistically, the only reason for using `add_reference<T>` instead of `T&` is `void`, and there we also have a bit of a problem - `add_const_reference<void>` is `void const` or just `void`?  
  
Might still be useful, we just need to clarify the behavior in those corner cases.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-05-21 13:29:40 UTC  
> Url: https://github.com/boostorg/type_traits/pull/34#issuecomment-302936772  

What was the feedback of  the proposed `uncvref` ?

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-05-21 13:43:21 UTC  
> Url: https://github.com/boostorg/type_traits/pull/34#issuecomment-302937570  

When I brought that up a few days ago, people raised a surprising (to me) amount of objections for such a small and obviously missing component. If only they were similarly diligent in screening other std:: proposals.

---
