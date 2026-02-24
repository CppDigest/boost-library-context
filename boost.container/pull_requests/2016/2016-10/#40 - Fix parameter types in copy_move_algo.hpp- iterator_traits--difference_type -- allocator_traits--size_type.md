# #40 Fix parameter types in copy_move_algo.hpp: iterator_traits::difference_type -> allocator_traits::size_type [Merged]

> Username: togermer  
> Created at: 2016-10-19 12:39:10 UTC  
> Updated at: 2016-11-12 23:41:35 UTC  
> Merged at: 2016-11-12 23:41:22 UTC  
> Closed at: 2016-11-12 23:41:22 UTC  
> Url: https://github.com/boostorg/container/pull/40  

Many of the functions inside copy_move_algo.hpp construct n objects using a specified Allocator, so the number of objects should be given as allocator_traits::size_type. The callers of these functions already use size_type, which currently leads to type conflicts if the InputIterator's difference_type is incompatible (e.g., smaller).   
  
There is one exception in vector.hpp, where uninitialized_move_alloc_n() is called with a raw pointer difference. I static_casted this to size_type for compatibility.  
  
There are several remaining functions inside copy_move_algo.hpp that use a difference_type parameter, but are independent of an allocator. I changed these to be agnostic of the integral type, as was already done with destroy_alloc_n(). (In fact, all of these functions eventually get called with allocator_traits::size_type arguments.)

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-11-12 23:41:35 UTC  
> Url: https://github.com/boostorg/container/pull/40#issuecomment-260156146  

Thanks for the pull!

---
