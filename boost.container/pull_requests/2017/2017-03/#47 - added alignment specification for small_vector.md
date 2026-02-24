# #47 added alignment specification for small_vector [Closed]

> Username: tobias-loew  
> Created at: 2017-03-27 10:23:25 UTC  
> Updated at: 2019-05-09 20:18:42 UTC  
> Closed at: 2019-05-09 20:16:03 UTC  
> Url: https://github.com/boostorg/container/pull/47  

Hi,  
in order to be able to use small_vectors for SSE aligned doubles, I've  
added an alignment specification for small_vector which ensures alignment  
of the internal-storage.   
The default value of 0 for the new template parameter ensures the original alignment-calculation.  
  
Tobias

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2017-03-28 11:15:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container/pull/47#pullrequestreview-29420313  

📁 include/boost/container/container_fwd.hpp

```diff
 118 |-          , class Allocator= new_allocator<T> >
 118 |+          , class Allocator= new_allocator<T>
 119 |+          , std::size_t alignment= 0 >
```

> Username: glenfe  
> Created_at: 2017-03-28 11:15:02 UTC  
> Updated_at: 2017-03-28 11:15:03 UTC  
> Url: https://github.com/boostorg/container/pull/47#discussion_r108393915  

If anything, the default value for the alignment value may as well be 1, indicating the minimum alignment value.

> Username: tobias-loew  
> Created_at: 2017-03-29 06:25:04 UTC  
> Url: https://github.com/boostorg/container/pull/47#discussion_r108599086  

Hi,  
I've deliberately defaulted alignment to 0 to be able to also align the storage below its natural alignment. I don't have any applications for that right now, but since the dynamic allocator has the ability, I thought it could be an option...


---

## Review 2 [Commented]

> Username: glenfe  
> Created_at: 2017-03-28 11:17:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container/pull/47#pullrequestreview-29420665  

📁 include/boost/container/small_vector.hpp

```diff
 420 |+    typedef small_vector_base<T, Allocator, alignment> svh_type;
 421 |+    typedef vector<T, small_vector_allocator<Allocator, alignment> > svhb_type;
 422 |+    static const std::size_t s_align = alignment > 0 ? alignment : container_detail::alignment_of<Storage>::value;
```

> Username: glenfe  
> Created_at: 2017-03-28 11:17:10 UTC  
> Updated_at: 2017-03-28 11:18:54 UTC  
> Url: https://github.com/boostorg/container/pull/47#discussion_r108394224  

With `alignment` defaulting to 1, this expression should just also be `static const std::size_t s_align = max_size<alignment, alignment_of<Storage>::value>::value;` or similar.


---

## Comment 3

> Username: igaztanaga  
> Created_at: 2019-05-09 20:18:42 UTC  
> Url: https://github.com/boostorg/container/pull/47#issuecomment-491050761  

Thanks for the report. I've added options to small_vector with the same philosophy as vector/deque/static_vector options. small_vector now can receive two options:  
  
inplace_alignment: similar to your proposal, the alignment for the inplace storage can be proposed.  
growth_factor: the growth factor for the dynamic allocation, the same option that can be specified in vector.  
  
Many thanks for the proposal.

---
