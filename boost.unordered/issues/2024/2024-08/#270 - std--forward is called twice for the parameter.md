# #270 - std::forward is called twice for the parameter [Closed]

> Username: NumberEndymion4  
> Created at: 2024-08-05 08:59:13 UTC  
> Updated at: 2024-08-06 13:48:14 UTC  
> Closed at: 2024-08-06 13:48:14 UTC  
> Url: https://github.com/boostorg/unordered/issues/270  

In `unordered_map::emplace(Args&&... args)` and `unordered_set::emplace(Args&&... args)` functions, `std::forward` is called twice for the variadic parameter `args`. This may cause the moved parameter to be used.

---

## Comment 1

> Username: k3DW  
> Created at: 2024-08-05 21:35:00 UTC  
> Url: https://github.com/boostorg/unordered/issues/270#issuecomment-2269959884  

In this particular case, there is no actual use-after-move. That said, it's confusing to read the code and see `std::forward` being called twice, so I opened a PR to use `as_const` instead of the first call to `std::forward`.
