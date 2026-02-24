# #28 Fix gcc 6 fail, when it tries to determine whether recursive_wrapper … [Merged]

> Username: very-cool-name  
> Created at: 2017-01-12 11:20:17 UTC  
> Updated at: 2017-01-13 22:25:31 UTC  
> Merged at: 2017-01-13 22:24:58 UTC  
> Closed at: 2017-01-13 22:24:58 UTC  
> Url: https://github.com/boostorg/variant/pull/28  

…is_nothrow_move_constructible  (trac #12680)

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2017-01-13 19:47:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/28#pullrequestreview-16637862  

📁 test/recursive_wrapper_move_test.cpp

```diff
   9 |+ #include "boost/test/minimal.hpp"
  10 |+ 
  11 |+ #if !defined(BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION)
```

> Username: apolukhin  
> Created_at: 2017-01-13 19:45:19 UTC  
> Updated_at: 2017-01-13 20:46:18 UTC  
> Url: https://github.com/boostorg/variant/pull/28#discussion_r96060604  

Use `#ifdef __cpp_inheriting_constructors`  
  
Those `__cpp*` macros are now part of the C++ Standard and they are provided by all the compatible compilers https://gcc.gnu.org/projects/cxx-status.html

---

📁 test/recursive_wrapper_move_test.cpp

```diff
  33 |+ 
  34 |+   template <typename Iter>
  35 |+   static Tree Create(Iter first, Iter last) { return Leaf{}; }
```

> Username: apolukhin  
> Created_at: 2017-01-13 19:46:21 UTC  
> Updated_at: 2017-01-13 20:46:18 UTC  
> Url: https://github.com/boostorg/variant/pull/28#discussion_r96060812  

Use `static Tree Create(Iter /*first*/, Iter /*last*/) { return Leaf{}; }`  
  
This will prevent unused variable warnings

---

📁 test/recursive_wrapper_move_test.cpp

```diff
  44 |+ 
  45 |+   const Tree root = Tree::Create(input.begin(), input.end());
  46 |+ }
```

> Username: apolukhin  
> Created_at: 2017-01-13 19:47:03 UTC  
> Updated_at: 2017-01-13 20:46:18 UTC  
> Url: https://github.com/boostorg/variant/pull/28#discussion_r96060976  

Use `(void)root` to avoid unused variable warning


---

## Comment 2

> Username: apolukhin  
> Created_at: 2017-01-13 22:25:31 UTC  
> Url: https://github.com/boostorg/variant/pull/28#issuecomment-272564874  

Great thanks for the fixes and tests!

---
