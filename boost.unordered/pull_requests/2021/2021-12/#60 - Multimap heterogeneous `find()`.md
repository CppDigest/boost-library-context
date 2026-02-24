# #60 Multimap heterogeneous `find()` [Merged]

> Username: cmazakas  
> Created at: 2021-12-15 22:23:21 UTC  
> Updated at: 2021-12-17 19:20:38 UTC  
> Merged at: 2021-12-17 19:06:17 UTC  
> Closed at: 2021-12-17 19:06:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/60  



---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2021-12-16 22:46:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/60#pullrequestreview-834675836  

📁 include/boost/unordered/detail/implementation.hpp

```diff
 716 |+       template <class X, class H, class P>
 717 |+       struct is_transparent2 {
 718 |+         static bool const value = is_transparent<X, H>::value && is_transparent<X, P>::value;
```

> Username: glenfe  
> Created_at: 2021-12-16 22:45:57 UTC  
> Updated_at: 2021-12-16 22:46:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/60#discussion_r770972212  

If `is_transparent` is only used in `is_transparent2` it does not need the first template parameter anymore.  
  
```  
template<class, class = void>  
struct is_transparent  
    : false_type { };  
      
template<class T>  
struct is_transparent<T, typename make_void<typename T::is_transparent>::type>  
  : true_type { };  
  
template<class, class A, class B>  
struct are_transparent  
    : conjunction<is_transparent<A>, is_transparent<B> > { };  
```


---
