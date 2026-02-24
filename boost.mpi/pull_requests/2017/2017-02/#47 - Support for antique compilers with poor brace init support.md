# #47 Support for antique compilers  with poor brace init support. [Merged]

> Username: aminiussi  
> Created at: 2017-02-24 12:39:05 UTC  
> Updated at: 2017-07-24 12:02:50 UTC  
> Merged at: 2017-07-23 08:22:10 UTC  
> Closed at: 2017-07-23 08:22:10 UTC  
> Url: https://github.com/boostorg/mpi/pull/47  

Tests passes based on serialization/develop and 2008 g++ compiler 4.3.1

---

## Comment 1

> Username: aminiussi  
> Created_at: 2017-02-28 12:46:43 UTC  
> Url: https://github.com/boostorg/mpi/pull/47#issuecomment-283029988  

Add support for even older compiler with no vector::data

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2017-07-02 10:24:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpi/pull/47#pullrequestreview-47546796  

📁 src/cartesian_communicator.cpp

```diff
  16 |+ namespace {
  17 |+   template <typename T, typename A>
  18 |+   T* c_data(std::vector<T,A>& v) { return &(v[0]); }
```

> Username: Lastique  
> Created_at: 2017-07-02 10:24:11 UTC  
> Updated_at: 2017-07-23 07:49:50 UTC  
> Url: https://github.com/boostorg/mpi/pull/47#discussion_r125179323  

Can this be replaced with `c_data` from antiques.hpp?

> Username: aminiussi  
> Created_at: 2017-07-03 09:32:43 UTC  
> Updated_at: 2017-07-23 07:49:50 UTC  
> Url: https://github.com/boostorg/mpi/pull/47#discussion_r125246805  

Yes, and it should actually.


---
