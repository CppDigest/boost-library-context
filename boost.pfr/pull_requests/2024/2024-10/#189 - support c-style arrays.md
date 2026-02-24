# #189 support c-style arrays [Open]

> Username: p00f  
> Created at: 2024-10-19 14:16:31 UTC  
> Updated at: 2025-03-06 19:07:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/189  

this tries to implement the solution in #170, with one change: I saw some "must be aggregate initializable" errors so I added another case depending on `std::is_aggregate_v<T>`

---

## Comment 1

> Username: p00f  
> Created_at: 2024-10-19 16:41:46 UTC  
> Url: https://github.com/boostorg/pfr/pull/189#issuecomment-2424062323  

the tests still fail though:   
[test.log](https://github.com/user-attachments/files/17446404/test.log)

---

## Comment 2

> Username: yrashk  
> Created_at: 2024-10-21 04:18:04 UTC  
> Url: https://github.com/boostorg/pfr/pull/189#issuecomment-2425545418  

Very interested in seeing this through, let me know if I can help in any way!

---

## Comment 3

> Username: apolukhin  
> Created_at: 2024-10-21 10:27:22 UTC  
> Url: https://github.com/boostorg/pfr/pull/189#issuecomment-2426266647  

> the tests still fail though:  
  
std::is_aggregate is not available in C++14, but just ignore that issue for now. Make sure that the PR builds in С++20 or C++17 mode. Any modern enough compiler is fine for that, clang-18 is fine.

---

## Review 4 [Commented]

> Username: denzor200  
> Created_at: 2025-03-06 19:07:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/189#pullrequestreview-2665386609  

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 150 | 
 151 |- template <class T, std::size_t I0, std::size_t... I, class /*Enable*/ = std::enable_if_t<std::is_copy_constructible<T>::value>>
 151 |+ template <class T, std::size_t I0, std::size_t... I, class /*Enable*/ = std::enable_if_t<std::is_copy_constructible<T>::value and !std::is_aggregate_v<T>>>
```

> Username: denzor200  
> Created_at: 2025-03-06 19:07:02 UTC  
> Updated_at: 2025-03-06 19:07:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/189#discussion_r1983912347  

please provide unit-test to check that `boost::pfr::tuple_size` for a structure with c-style array field returns real count of fields


---
