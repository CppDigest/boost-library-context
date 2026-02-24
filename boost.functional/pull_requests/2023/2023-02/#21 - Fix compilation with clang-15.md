# #21 Fix compilation with clang-15 [Open]

> Username: dimula73  
> Created at: 2023-02-16 12:08:52 UTC  
> Updated at: 2023-06-21 05:14:38 UTC  
> Url: https://github.com/boostorg/functional/pull/21  

std::unary_function and std::binary_function are removed from the standard in C++17.

---

## Comment 1

> Username: glenfe  
> Created_at: 2023-04-13 04:23:21 UTC  
> Updated_at: 2023-04-13 14:08:55 UTC  
> Url: https://github.com/boostorg/functional/pull/21#issuecomment-1506323125  

I think we should just define these unconditionally for now. I can take care of it, for 1.83. If needed we can provide a patch for clang 15 users for 1.82 on the website.

---

## Comment 2

> Username: alvinhochun  
> Created_at: 2023-04-13 15:30:46 UTC  
> Url: https://github.com/boostorg/functional/pull/21#issuecomment-1507176457  

For reference, `BOOST_NO_CXX98_FUNCTION_BASE` is defined in:  
https://github.com/boostorg/config/commit/0e364efe0b2397cffd952dbaf80577609bf1058f  
https://github.com/boostorg/config/pull/430  
https://github.com/boostorg/config/pull/440

---

## Comment 3

> Username: JIghtuse  
> Created_at: 2023-06-21 05:14:38 UTC  
> Url: https://github.com/boostorg/functional/pull/21#issuecomment-1600084795  

Please fix it, it breaks parts of boost which depend on functional. See [this example](https://godbolt.org/z/8nGjGa3x8) with `multi_array`: user needs to pass weird `-D_HAS_AUTO_PTR_ETC=0` to fix compilation with Clang + libc++.

---
