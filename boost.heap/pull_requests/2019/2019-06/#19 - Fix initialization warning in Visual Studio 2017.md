# #19 Fix initialization warning in Visual Studio 2017 [Merged]

> Username: martinfalk  
> Created at: 2019-06-16 08:12:38 UTC  
> Updated at: 2019-06-17 09:17:00 UTC  
> Merged at: 2019-06-17 01:54:27 UTC  
> Closed at: 2019-06-17 01:54:27 UTC  
> Url: https://github.com/boostorg/heap/pull/19  

This fixes warning C5038 in Visual Studio 2017, which is caused by out-of-order initialization in the constructors of `heap_base`.  
```  
C:\dev\boost_1_70_0\boost/heap/detail/stable_heap.hpp(186):   
warning C5038: data member 'boost::heap::detail::heap_base<T,std::function<bool (ttk::SimplexId,ttk::SimplexId)>,true,unsigned __int64,false>::cmp_' will be initialized after base class 'boost::heap::detail::size_holder<true,size_t>' with [ T=ttk::SimplexId ]  
```

---

## Review 1 [Approved]

> Username: timblechmann  
> Created_at: 2019-06-16 08:23:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/heap/pull/19#pullrequestreview-250221270  

lgtm, would be curious if we can avoid the duplicate `ifdef`, though ...

📁 include/boost/heap/detail/stable_heap.hpp

```diff
 177 |-         cmp_(std::move(rhs.cmp_)),
 176 | #endif
 177 |         size_holder_type(std::move(static_cast<size_holder_type&>(rhs)))
```

> Username: timblechmann  
> Created_at: 2019-06-16 08:22:39 UTC  
> Updated_at: 2019-06-16 08:23:12 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294073300  

is it possibly to move the initialisation of `size_holder_type` up? or would this result in a similar warning?

> Username: martinfalk  
> Created_at: 2019-06-16 08:54:46 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294074564  

I assume that this would result in a similar warning for gcc and clang.

> Username: martinfalk  
> Created_at: 2019-06-16 08:56:42 UTC  
> Updated_at: 2019-06-16 08:59:02 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294074627  

One could however duplicate the `size_holder_type` in both cases of the `ifdef`, i.e.  
```c++  
#ifndef BOOST_MSVC  
        Cmp(static_cast<Cmp const &>(rhs)),  
        size_holder_type(static_cast<size_holder_type const &>(rhs))  
#else  
        size_holder_type(static_cast<size_holder_type const &>(rhs)),  
        cmp_(rhs.value_comp())  
#endif  
```

> Username: martinfalk  
> Created_at: 2019-06-16 09:02:09 UTC  
> Updated_at: 2019-06-16 09:02:10 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294074806  

Another alternative might be to let `struct heap_base` derive first from `size_holder<...>` and then from `Cmp` for non-MSVC compilers. Not sure, though, whether this will have implications somewhere else.

> Username: martinfalk  
> Created_at: 2019-06-16 09:04:37 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294074873  

Then we could leave the constructors unchanged, i.e.  
```c++  
#ifndef BOOST_MSVC  
        Cmp(std::move(static_cast<Cmp&>(rhs))),  
#else  
        cmp_(std::move(rhs.cmp_)),  
#endif  
        size_holder_type(std::move(static_cast<size_holder_type&>(rhs)))  
```

> Username: timblechmann  
> Created_at: 2019-06-17 01:55:08 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294115268  

probably not worth the effort to silence a small warning ...  
  
thanks a lot for the PR!

> Username: martinfalk  
> Created_at: 2019-06-17 06:09:41 UTC  
> Url: https://github.com/boostorg/heap/pull/19#discussion_r294146450  

You are welcome!


---
