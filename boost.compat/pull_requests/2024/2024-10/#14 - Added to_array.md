# #14 Added to_array [Merged]

> Username: anarthal  
> Created at: 2024-10-03 03:57:50 UTC  
> Updated at: 2024-10-04 10:06:44 UTC  
> Merged at: 2024-10-04 10:06:44 UTC  
> Closed at: 2024-10-04 10:06:44 UTC  
> Url: https://github.com/boostorg/compat/pull/14  



---

## Comment 1

> Username: pdimov  
> Created_at: 2024-10-03 08:31:15 UTC  
> Url: https://github.com/boostorg/compat/pull/14#issuecomment-2390829157  

1. the static asserts don't actually test what the code requires. The array elements are of type `remove_cv_t<T>`, not `T`, so the conditions being asserted should be `is_constructible<remove_cv_t<T>, T&>` and `is_constructible<remove_cv_t<T>, T&&>` respectively.  
2. documentation  
3. the tests should test const input elements, e.g. `int const`, `std::vector<int> const`.  
4. the constexpr tests should still check that the result is correct (at runtime).

---

## Comment 2

> Username: anarthal  
> Created_at: 2024-10-03 11:19:35 UTC  
> Url: https://github.com/boostorg/compat/pull/14#issuecomment-2391162437  

Regarding 1. [cppreference](https://en.cppreference.com/w/cpp/container/array/to_array) it states it as I wrote it - is it a defect in cppreference?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-10-03 11:37:11 UTC  
> Url: https://github.com/boostorg/compat/pull/14#issuecomment-2391200278  

cppreference correctly reflects what the standard says in https://eel.is/c++draft/array.creation, but I think that this is a defect in the standard. I'll ask.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-10-03 18:20:01 UTC  
> Url: https://github.com/boostorg/compat/pull/14#issuecomment-2392042592  

Please add lvalue tests for `const int` and l/rvalue tests for const.  
  
Also, let's use the correct `is_constructible<remove_cv_t<T>, T&>` in both code and docs, regardless of what the standard says.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-10-03 19:06:51 UTC  
> Url: https://github.com/boostorg/compat/pull/14#issuecomment-2392130423  

No, sorry. The array elements being constructed are of type `remove_cv_t<T>`. The arguments are of type `T&` or `T&&` respectively. Ergo, the conditions are `is_constructible<remove_cv_t<T>, T&>` and `is_constructible<remove_cv_t<T>, T&&>`, respectively.

---

## Comment 6

> Username: anarthal  
> Created_at: 2024-10-04 09:50:54 UTC  
> Url: https://github.com/boostorg/compat/pull/14#issuecomment-2393310027  

Long day yesterday. Should be fixed now. If I've understood this correctly, using `remove_cvref_t` doesn't make sense because C arrays can't contain references, and thus `remove_cv_t` is more accurate.  
  
I've left the multi-dimensional checking condition as `is_array<T>` because `is_array<const/volatile int[N]>` is also true.

---
