# #53 Most members of std::allocate are deprecated in C++17 [Closed]

> Username: DanielaE  
> Created at: 2017-12-31 16:47:05 UTC  
> Updated at: 2020-08-12 06:27:48 UTC  
> Closed at: 2020-08-12 06:27:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/53  

Replace them by their cousins from std::allocator_traits.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2020-05-31 14:17:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/53#pullrequestreview-421499114  

📁 include/boost/numeric/ublas/matrix.hpp

```diff
3444 |+         typedef std::allocator_traits<ALLOC> alloc_traits;
3445 |+         typedef typename alloc_traits::size_type size_type;
3446 |+         typedef typename alloc_traits::difference_type difference_type;
```

> Username: glenfe  
> Created_at: 2020-05-31 14:16:49 UTC  
> Updated_at: 2020-05-31 14:17:32 UTC  
> Url: https://github.com/boostorg/ublas/pull/53#discussion_r432951681  

We have `<boost/core/allocator_access.hpp>`  now which simplifies this. Instead of:  
  
```  
#ifdef BOOST_NO_CXX11_ALLOCATOR  
typedef typename ALLOC::size_type size_type;  
#else  
typedef typename std::allocator_traits<ALLOC>::size_type size_type;  
#endif   
```  
This can be simply:  
```  
typedef typename boost::allocator_size_type<ALLOC>::type size_type;  
```  
It is our equivalent of `allocator_traits` just with individual traits and functions.


---

## Comment 2

> Username: glenfe  
> Created_at: 2020-08-12 06:27:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/53#issuecomment-672636293  

Fixed in develop with #103.

---
