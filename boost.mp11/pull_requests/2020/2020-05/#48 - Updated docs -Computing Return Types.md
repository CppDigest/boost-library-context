# #48 Updated docs "Computing Return Types" [Merged]

> Username: slymz  
> Created at: 2020-05-04 21:37:36 UTC  
> Updated at: 2020-05-23 19:06:55 UTC  
> Merged at: 2020-05-23 19:06:55 UTC  
> Closed at: 2020-05-23 19:06:55 UTC  
> Url: https://github.com/boostorg/mp11/pull/48  

Suggest `std::invoke_result` as an in place modern alternative for `Qret`.  
Fix bug with `const variant` calls, using `std::decay_t` instead of `std::remove_reference_t`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-05-04 21:44:51 UTC  
> Url: https://github.com/boostorg/mp11/pull/48#issuecomment-623724439  

std::invoke_result is C++17.  
  
What is the bug with const variant calls? Do you have a code example that demonstrates it?

---

## Comment 2

> Username: slymz  
> Created_at: 2020-05-05 14:04:25 UTC  
> Url: https://github.com/boostorg/mp11/pull/48#issuecomment-624075586  

> std::invoke_result is C++17.  
  
so is std::variant.  
  
> What is the bug with const variant calls?  
  
Same as in your Fixing tuple_cat fix.  `mp_*<V>` won't work when V is `const std::variant<...>`.  Modify `main()`'s first line to have `const std::variant` and you'll see.

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-05-23 18:48:09 UTC  
> Url: https://github.com/boostorg/mp11/pull/48#issuecomment-633115936  

Good points, thanks.

---
