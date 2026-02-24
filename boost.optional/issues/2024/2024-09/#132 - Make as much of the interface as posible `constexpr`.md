# #132 - Make as much of the interface as posible `constexpr` [Closed]

> Username: akrzemi1  
> Created at: 2024-09-12 21:02:52 UTC  
> Updated at: 2026-02-18 20:28:56 UTC  
> Closed at: 2026-02-18 20:28:56 UTC  
> Url: https://github.com/boostorg/optional/issues/132  

Make as many functions as possible in the interface of `optional` `constexpr`. It is not possible to be as `constexpr` as `std::opitonal`, because the latter is C++17, where `constexpr` is mor powerful, whereas `boost::optional` needs to be C++11-compatible.  
  
This requires changing the implementation of optional storage to a `union`-based.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-09-14 22:39:46 UTC  
> Url: https://github.com/boostorg/optional/issues/132#issuecomment-2351201572  

For a `union`-based storage, see this implementation of Optional: https://github.com/akrzemi1/Optional

---

## Comment 2

> Username: typenametea  
> Created at: 2024-09-14 23:09:26 UTC  
> Url: https://github.com/boostorg/optional/issues/132#issuecomment-2351210064  

Hello, I'll take a look into implementing this :slightly_smiling_face:

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-09-14 23:11:45 UTC  
> Url: https://github.com/boostorg/optional/issues/132#issuecomment-2351211002  

As indicated in https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3527.html#rationale.constexpr the constexprification can only work fro `T`s that are literal types, and still, you cannot do it for the copy and move constructors in C++11. But you can constexprify them in later C++ verisons.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2026-02-18 20:28:56 UTC  
> Url: https://github.com/boostorg/optional/issues/132#issuecomment-3923050028  

Addressed in https://github.com/boostorg/optional/issues/143.
