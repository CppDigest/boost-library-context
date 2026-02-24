# #5 - Add `boost::move_only_function` for C++11 [Closed]

> Username: denzor200  
> Created at: 2023-08-24 23:07:42 UTC  
> Updated at: 2025-06-28 14:52:07 UTC  
> Closed at: 2025-06-28 14:52:07 UTC  
> Url: https://github.com/boostorg/compat/issues/5  

Analogue of `std`'s one ( https://en.cppreference.com/w/cpp/utility/functional/move_only_function )

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-30 19:58:27 UTC  
> Url: https://github.com/boostorg/compat/issues/5#issuecomment-2028455680  

There have been a few requests for this already. It's a bit borderline because Compat is supposed to not contain interface types as they would cause ABI issues if selectively mapped to `std` counterparts with `using`, but user demand for `move_only_function` probably justifies bending the rules for it.

---

## Comment 2

> Username: fsmoke  
> Created at: 2025-05-26 15:29:09 UTC  
> Url: https://github.com/boostorg/compat/issues/5#issuecomment-2910106855  

I really need it too!
