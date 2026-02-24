# #74 Implement allocator utilities [Merged]

> Username: glenfe  
> Created at: 2020-04-14 12:17:00 UTC  
> Updated at: 2020-04-14 22:14:03 UTC  
> Merged at: 2020-04-14 22:14:03 UTC  
> Closed at: 2020-04-14 22:14:03 UTC  
> Url: https://github.com/boostorg/core/pull/74  

Instead of `allocator_traits` in Core, the same functionality provided by individual templates. This reflects recent C++ standard committee reflector discussion around how `allocator_traits` would be designed today instead.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2020-04-14 15:31:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/74#pullrequestreview-393045583  

📁 doc/allocator_access.qbk

```diff
  36 |+ template<class T, class A = boost::default_allocator<T> >
  37 |+ class container
  38 |+     : boost::emtpy_value<typename boost::allocator_rebind<A, T>::type> {
```

> Username: pdimov  
> Created_at: 2020-04-14 15:31:00 UTC  
> Updated_at: 2020-04-14 18:53:07 UTC  
> Url: https://github.com/boostorg/core/pull/74#discussion_r408230738  

emtpy


---
