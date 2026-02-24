# #17 update for ticket #11927 [Merged]

> Username: akumta  
> Created at: 2016-01-20 19:15:52 UTC  
> Updated at: 2016-11-30 09:58:07 UTC  
> Merged at: 2016-02-12 19:09:48 UTC  
> Closed at: 2016-02-12 19:09:48 UTC  
> Url: https://github.com/boostorg/optional/pull/17  

std::swap for C++ 03 is located under <algorithm> and for C++11 under <utility>

---

## Comment 1

> Username: akumta  
> Created_at: 2016-11-29 18:30:22 UTC  
> Url: https://github.com/boostorg/optional/pull/17#issuecomment-263656072  

Can this fix please be putback into Boost 1.63 master branch?

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2016-11-30 09:57:27 UTC  
> Updated_at: 2016-11-30 09:58:07 UTC  
> Url: https://github.com/boostorg/optional/pull/17#issuecomment-263831399  

This fix contains of two parts:  
  
1. A conditional header inclusion  
2. A conditional noexcept annotation in specializations.  
  
No 1 (conditional header) is already included in the file.  
No 2 (conditional noexcept in specializations) are incorrect in the fix, therefore I refuse to apply them, unless you can convince me that I am wrong.  
  
The master template of std::swap has the following noexcept specification:  
  
```c++  
noexcept(noexcept(  
  std::is_nothrow_move_constructible<T>::value &&  
  std::is_nothrow_move_assignable<T>::value  
))  
```  
  
In case, where `T` is either `optional_swap_test::class_without_default_ctor` or `optional_swap_test::class_whose_default_ctor_should_not_be_used`, the above condition evaluates to `noexcept(false)`. Therefore the specializations for `std::swap` should have the same noexcept-specification `noexcept(false)` which is equivalent to no noexcept-secification. The test case does exactly this. And if I change to what you propose, I would have a malformed C++ file.   
  
Does this explanation make sense?

---
