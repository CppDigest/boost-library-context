# #47 Workaround -Wunused-parameter [Merged]

> Username: georgthegreat  
> Created at: 2024-12-20 11:29:46 UTC  
> Updated at: 2024-12-27 01:00:29 UTC  
> Merged at: 2024-12-27 01:00:29 UTC  
> Closed at: 2024-12-27 01:00:29 UTC  
> Url: https://github.com/boostorg/heap/pull/47  



---

## Review 1 [Commented]

> Username: timblechmann  
> Created_at: 2024-12-20 11:56:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/heap/pull/47#pullrequestreview-2517400835  

📁 include/boost/heap/detail/heap_comparison.hpp

```diff
  27 | bool value_equality( Heap1 const& lhs, Heap2 const& rhs, typename Heap1::value_type lval, typename Heap2::value_type rval )
  28 | {
  29 |+ 	[[maybe_unused]] rhs;
```

> Username: timblechmann  
> Created_at: 2024-12-20 11:56:24 UTC  
> Url: https://github.com/boostorg/heap/pull/47#discussion_r1893851996  

maybe_unused is c++17, this library is still c++14. `boost::ignore_unused` would be more compatible.  
  
also, please use spaces instead of tabs (or ideally use pre-commit to format the code


---

## Comment 2

> Username: georgthegreat  
> Created_at: 2024-12-20 12:28:44 UTC  
> Url: https://github.com/boostorg/heap/pull/47#issuecomment-2556913323  

The syntax turned out to be incorrect, so I changed it to `(void)rhs;`

---
