# #320 - Add an array container that would store only a pointer to the elements and a size [Open]

> Username: aandrejevas  
> Created at: 2025-10-24 20:50:17 UTC  
> Updated at: 2025-10-24 20:50:17 UTC  
> Url: https://github.com/boostorg/container/issues/320  

This proposed array container type would store a pointer to the allocated memory, and the number of elements that allocated memory contains. The allocator type would be provided as a template parameter, and the default allocator would allocate the memory on the heap.  
  
Currently, using the standard library, it is possible to replicate this functionality in two ways but they both have their drawbacks, which makes them painful to use.  
  
The first solution is to use `std::vector`. The problem with this solution is that `std::vector` stores an additional capacity member field, which serves no purpose. Also, it can allocate more elements than requested anticipating that additional elements will get pushed to it. This means that always some memory is going to be wasted when using `std::vector` instead of the proposed array. There is also the problem of intentionality with this solution. We are not being intentional when using `std::vector` instead of the proposed array, because to the reader of the code it is not clear whether we will ever push something else to the `std::vector` (`std::vector` has a lot of additional functionality which serves no purpose in this case) while it would be clear that we will not be pushing anything to the proposed array because it does not have such functionality.  
  
The second solution is to use `std::unique_ptr`. The problem with this solution is that `std::unique_ptr` only stores a pointer and does not store the size. That means that the size would have to be stored in a separate variable and that makes this solution really inconvenient and painful to use. Imagine a function that accepts a `std::span`, so when calling this function we would always need to construct the span from a `std::unique_ptr` and the size variable that is stored separately. While with the proposed array, we could just pass it to the function. There is also the additional problem that there is no standard way to use the allocators with `std::unique_ptr`, there is no allocator support for `std::unique_ptr`. That means that this solution does not even fully replicate the functionality of the proposed array.  
  
boost does not already provide such a container. The name of this proposed array container type should still be decided.
