# #23 - Is it more consistent if `mp_for_each` invokes functor with `mp_identity<T>()`? [Open]

> Username: oliora  
> Created at: 2018-07-02 17:41:34 UTC  
> Updated at: 2019-05-13 21:49:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/23  

Currently, `mp_for_each` invokes `f` with `T()`. This adds a default-constructible requirement for type `T` which is not always feasible and also not very consistent with other parts of mp11 that never instantiate user-provided types. Should not `mp_for_each` invoke passed functor with `mp_identity<T>()` instead?

---

## Comment 1

> Username: pdimov  
> Created at: 2018-07-02 21:43:29 UTC  
> Url: https://github.com/boostorg/mp11/issues/23#issuecomment-401947179  

`mp_identity` is often what you want, but not always; when the list contains integral constants, you don't want them wrapped. I went back and forth on adding the `mp_identity` a few times and at the end decided not to. We could add `mp_for_each_id<L>` that does `mp_for_each<mp_transform<mp_identity, L>>` even though I don't mind spelling that out.

---

## Comment 2

> Username: oliora  
> Created at: 2018-07-03 15:25:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/23#issuecomment-402196425  

`mp_for_each_identity<L>` or `.._id` sounds good as well.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-05-13 19:46:32 UTC  
> Url: https://github.com/boostorg/mp11/issues/23#issuecomment-491960037  

Just passing by with a comment: I slightly lean against adding `mp_for_each_id` because it is trivial to write, but I am in favour of explicitly pointing out the construction `mp_for_each<mp_transform<mp_identity, L>>` in an example in the documentation of `mp_for_each`.

---

## Comment 4

> Username: oliora  
> Created at: 2019-05-13 21:49:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/23#issuecomment-491999931  

I think, extending the documentation but no code changes is a good trade-off.
