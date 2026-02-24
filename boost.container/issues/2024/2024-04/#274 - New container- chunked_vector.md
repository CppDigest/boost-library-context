# #274 - New container: chunked_vector [Open]

> Username: HDembinski  
> Created at: 2024-04-24 14:37:01 UTC  
> Updated at: 2024-05-23 22:01:36 UTC  
> Url: https://github.com/boostorg/container/issues/274  

For Boost.Histogram, I need a new type of container. I plan to implement a private version to keep histogram decoupled from Boost.Container, but I wanted to propose this type of container for Boost.Container.  
  
The container should store chunks of homogenous types with fixed length, which is defined at runtime, not compile time.  
  
The container is internally implemented like std::vector, memory is contiguous. The element of the container is not a single value, but a chunk of values with fixed size, which is defined at runtime when the container is created. The value type of the container should be static_vector or small_vector, while its reference types should based on boost::span.  
  
Basically, this is a much more efficient version of `std::vector<std::vector<T>>` for the case when you know that the inner vector will always have same length, but you don't know this length at compile-time, so you cannot use `std::vector<std::array<T, N>>`.  
  
I tentatively call this `chunked_vector`, but I hope you come up with a better name.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-05-23 22:01:35 UTC  
> Url: https://github.com/boostorg/container/issues/274#issuecomment-2128090563  

This seems like a quite specialized container, I don't feel it's general enough for Boost.Container. One option would be to add the option to dynamically specify the size of the chunk ("block", in boost::container::deque terminology). Currently the block size can be specified at compile-time, but if it could be specified at runtime, then a specialized container like yours would be easy to implement.
