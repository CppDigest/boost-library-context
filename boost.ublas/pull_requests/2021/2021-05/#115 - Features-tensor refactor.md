# #115 Features/tensor refactor [Merged]

> Username: bassoy  
> Created at: 2021-05-23 13:06:51 UTC  
> Updated at: 2021-05-24 18:52:04 UTC  
> Merged at: 2021-05-24 10:00:38 UTC  
> Closed at: 2021-05-24 10:00:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/115  

1. Renamed and restructured extents types  
2. Removed type traits for extents and strides types  
3. Removed the old strides type. Strides are now the base type of the corresponding extents type (resolving issue #111 )  
4. Created common non-member functions for extents  
5. Created special non-member for constexpr evaluation of static extents traits  
6. Created new Matlab like functions such as `ones` and `zeros`  
7. Removed old CI files for travis and appveyor  
8. Removed unnecessary CI badges in README

---

## Review 1 [Commented]

> Username: amitsingh19975  
> Created_at: 2021-05-23 13:16:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/115#pullrequestreview-666267676  

One step closer to improve the uBLAS.

---

## Review 2 [Commented]

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/115#pullrequestreview-666270070  

Reports by clang tidy

📁 test/tensor/test_static_tensor_matrix_vector.cpp

```diff
 443 |             auto n = e[1];
 447 |-             auto ab = n * (n+1) / 2;
 444 |+             auto ab = value(n*(n+1)/2);
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:32 UTC  
> Updated_at: 2021-05-23 13:43:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548871  

result of integer division used in a floating point context; possible loss of precision [bugprone-integer-division]  
```cpp  
auto ab = value(n*(n+1)/2);  
                            ^  
```  
Reported as warning by clang-tidy.

> Username: bassoy  
> Created_at: 2021-05-23 21:56:03 UTC  
> Updated_at: 2021-05-23 21:56:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637606440  

Is fixed now using `std::div`


📁 test/tensor/test_tensor_matrix_vector.cpp

```diff
 445 |             auto n = e[1];
 445 |-             auto ab = n * (n+1) / 2;
 446 |+             auto ab = value(n * (n+1) / 2);
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:32 UTC  
> Updated_at: 2021-05-23 13:43:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548872  

result of integer division used in a floating point context; possible loss of precision [bugprone-integer-division]  
```cpp  
auto ab = value(n * (n+1) / 2);  
                            ^  
```  
Reported as warning by clang-tidy.

> Username: bassoy  
> Created_at: 2021-05-23 21:56:10 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637606458  

Is fixed now using `std::div`


📁 include/boost/numeric/ublas/tensor/extents/extents_dynamic_size.hpp

```diff
  37 |+  */
  38 |+ template<integral T>
  39 |+ class extents_core<T>
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548873  

class `extents_core` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,hicpp-special-member-functions]  
```cpp  
class extents_core<T>  
      ^  
```  
Reported as warning by clang-tidy.

> Username: bassoy  
> Created_at: 2021-05-23 22:01:19 UTC  
> Updated_at: 2021-05-23 22:02:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637606920  

Implemented the copy-swap idiom for the [assignment operator](https://en.cppreference.com/w/cpp/language/copy_assignment) using pass by value. Clang-Tidy does not see it.


📁 include/boost/numeric/ublas/tensor/extents/extents_static_size.hpp

```diff
  40 |+  */
  41 |+ template <integral T, T N>
  42 |+ class extents_core<T,N> : public extents_base<extents_core<T,N>>
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548874  

class `extents_core` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,hicpp-special-member-functions]  
```cpp  
class extents_core<T,N> : public extents_base<extents_core<T,N>>  
      ^  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/tensor/tensor_dynamic.hpp

```diff
  38 |+ 
  39 |+ template<class V, class L>
  40 |+   class tensor_core<engine_tensor_dynamic<V,L>>
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:33 UTC  
> Updated_at: 2021-05-23 13:43:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548875  

class `tensor_core` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,hicpp-special-member-functions]  
```cpp  
class tensor_core<engine_tensor_dynamic<V,L>>  
        ^  
```  
Reported as warning by clang-tidy.

> Username: bassoy  
> Created_at: 2021-05-23 22:02:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637607056  

Implemented the copy-swap idiom for the [assignment operator](https://en.cppreference.com/w/cpp/language/copy_assignment) using pass by value. Clang-Tidy does not see it.


📁 include/boost/numeric/ublas/tensor/tensor/tensor_static.hpp

```diff
  43 |+ namespace boost::numeric::ublas {
  44 |+ template<class V, class L, std::size_t ... ns>
  45 |+ class tensor_core<detail::engine_tensor_static<V,L,ns...>>
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:33 UTC  
> Updated_at: 2021-05-23 13:43:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548876  

class `tensor_core` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,hicpp-special-member-functions]  
```cpp  
class tensor_core<detail::engine_tensor_static<V,L,ns...>>  
      ^  
```  
Reported as warning by clang-tidy.

> Username: bassoy  
> Created_at: 2021-05-23 22:02:23 UTC  
> Updated_at: 2021-05-23 22:02:24 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637607078  

Implemented the copy-swap idiom for the [assignment operator](https://en.cppreference.com/w/cpp/language/copy_assignment) using pass by value. Clang-Tidy does not see it.


📁 include/boost/numeric/ublas/tensor/tensor/tensor_static_rank.hpp

```diff
  39 |+ 
  40 |+ template<class V, class L, std::size_t N>
  41 |+   class tensor_core<engine_tensor_static_rank<V,L,N>>
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 13:43:33 UTC  
> Updated_at: 2021-05-23 13:43:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637548877  

class `tensor_core` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,hicpp-special-member-functions]  
```cpp  
class tensor_core<engine_tensor_static_rank<V,L,N>>  
        ^  
```  
Reported as warning by clang-tidy.

> Username: bassoy  
> Created_at: 2021-05-23 22:02:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637607088  

Implemented the copy-swap idiom for the [assignment operator](https://en.cppreference.com/w/cpp/language/copy_assignment) using pass by value. Clang-Tidy does not see it.


---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-05-23 21:58:31 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#issuecomment-846630192  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Comment 4

> Username: bassoy  
> Created_at: 2021-05-23 22:03:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#issuecomment-846630670  

> improve

---

## Review 5 [Commented]

> Username: github-actions[bot]  
> Created_at: 2021-05-23 22:39:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/115#pullrequestreview-666311504  

Reports by clang tidy

📁 test/tensor/test_static_tensor_matrix_vector.cpp

```diff
 442 |+         auto n = e[1];
 443 |+         auto ab = value(std::div(n*(n+1),2).quot);
 444 |+         const auto ref = ab+4*Q(0)+2*c(0);
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 22:39:39 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637610821  

result of integer division used in a floating point context; possible loss of precision [bugprone-integer-division]  
```cpp  
auto ab = value(n*(n+1)/2);  
                            ^  
```  
Reported as warning by clang-tidy.


📁 test/tensor/test_tensor_matrix_vector.cpp

```diff
 444 |+         const auto n   = e[1];
 445 |+         const auto ab  = value(std::div(n*(n+1),2).quot);
 446 |+         const auto ref = ab+4*Q(0)+2*c(0);
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 22:39:39 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637610822  

result of integer division used in a floating point context; possible loss of precision [bugprone-integer-division]  
```cpp  
auto ab = value(n * (n+1) / 2);  
                            ^  
```  
Reported as warning by clang-tidy.


---

## Review 6 [Commented]

> Username: github-actions[bot]  
> Created_at: 2021-05-23 23:13:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/115#pullrequestreview-666314162  

Reports by clang tidy

📁 examples/tensor/multiply_tensors_einstein_notation.cpp

```diff
  31 |+   using namespace boost::numeric::ublas::index;
  32 |+ 
  33 |+   using namespace boost::numeric::ublas::index;
```

> Username: github-actions[bot]  
> Created_at: 2021-05-23 23:13:27 UTC  
> Url: https://github.com/boostorg/ublas/pull/115#discussion_r637614483  

do not use namespace using-directives; use using-declarations instead [google-build-using-namespace]  
```cpp  
using namespace boost::numeric::ublas::index;  
  ^  
```  
Reported as warning by clang-tidy.


---
