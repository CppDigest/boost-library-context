# #90 stack/spsc_queue - introduce move semantics [Merged]

> Username: timblechmann  
> Created at: 2023-11-04 02:57:27 UTC  
> Updated at: 2024-05-04 02:15:39 UTC  
> Merged at: 2024-05-04 02:15:31 UTC  
> Closed at: 2024-05-04 02:15:31 UTC  
> Url: https://github.com/boostorg/lockfree/pull/90  



---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-01-29 00:13:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lockfree/pull/90#pullrequestreview-1847669599  

📁 include/boost/lockfree/detail/freelist.hpp

```diff
  87 |     template < bool ThreadSafe, bool Bounded, typename ArgumentType >
  88 |-     T* construct( ArgumentType const& arg )
  88 |+     T* construct( const ArgumentType& arg )
```

> Username: jeremy-murphy  
> Created_at: 2024-01-29 00:13:32 UTC  
> Updated_at: 2024-01-29 00:13:33 UTC  
> Url: https://github.com/boostorg/lockfree/pull/90#discussion_r1468971219  

Why are you going over to the dark side?  :)


---
