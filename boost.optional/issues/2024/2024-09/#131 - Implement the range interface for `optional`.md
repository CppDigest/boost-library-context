# #131 - Implement the range interface for `optional` [Open]

> Username: akrzemi1  
> Created at: 2024-09-09 21:04:59 UTC  
> Updated at: 2024-09-10 05:59:11 UTC  
> Url: https://github.com/boostorg/optional/issues/131  

Implement [P3168R2](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p3168r2.html) in Boost.Optional in a C++11-compatible manner.

---

## Comment 1

> Username: typenametea  
> Created at: 2024-09-09 21:36:55 UTC  
> Url: https://github.com/boostorg/optional/issues/131#issuecomment-2339198433  

Thanks for raising the issue :slightly_smiling_face:   
  
I'll be looking into implementing this

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-09-10 05:42:25 UTC  
> Url: https://github.com/boostorg/optional/issues/131#issuecomment-2339668336  

Thank you!

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-09-10 05:59:09 UTC  
> Url: https://github.com/boostorg/optional/issues/131#issuecomment-2339707193  

The following are criteria for a successful implementation.  
  
- [ ] Boost.Optional works for objects (implementation is in [here](https://github.com/boostorg/optional/blob/develop/include/boost/optional/optional.hpp)), and is also specialized for lvalue references (implementation is [here](https://github.com/boostorg/optional/blob/develop/include/boost/optional/detail/optional_reference_spec.hpp)). The range interface should also work for optional references.  
- [ ] Unit tests, apart from testing the presence of new members and their type, should also demonstrate that optional objects and optioal references can work with a range-based `for`-loop.  
- [ ] Paper [P3168r2](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p3168r2.html) shows examples how the new interface of `optional` interoperates with C++20 ranges. It would be nice to have such tests also. I do not consider them absolutely necessary. But these tests should be conditionally compiled, as otherwise they would break on pre-C++20 compilers.  
- [ ] The "reference" part of the documentation should reflect the new interface. I mean files [27_ref_optional_synopsis.qbk](https://github.com/boostorg/optional/blob/develop/doc/27_ref_optional_synopsis.qbk) and [28_ref_optional_semantics.qbk](https://github.com/boostorg/optional/blob/develop/doc/28_ref_optional_semantics.qbk).
