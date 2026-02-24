# #596 - No object::shrink_to_fit? [Open]

> Username: vinniefalco  
> Created at: 2021-07-25 19:20:46 UTC  
> Updated at: 2024-04-11 17:56:45 UTC  
> Url: https://github.com/boostorg/json/issues/596  

Why is there no `object::shrink_to_fit` ?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-04-08 17:45:09 UTC  
> Url: https://github.com/boostorg/json/issues/596#issuecomment-2043319375  

Do any associative containers have that? I've checked Unordered, and it's do not.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-04-08 19:22:58 UTC  
> Url: https://github.com/boostorg/json/issues/596#issuecomment-2043494670  

the question is not if associative containers have that, but rather whether vectors and vector-like containers have that. `json::object` is not node-based it is linear

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-04-11 17:56:35 UTC  
> Updated at: 2024-04-11 17:56:45 UTC  
> Url: https://github.com/boostorg/json/issues/596#issuecomment-2050215378  

`boost::unordered_flat_map` has `rehash` which apprarently can do the equivalent of `shrink_to_fit` and more. Maybe we should explore this.
