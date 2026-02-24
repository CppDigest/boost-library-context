# #280 - Several containers don't support non-movable types when move assigning [Closed]

> Username: igaztanaga  
> Created at: 2024-05-23 21:13:48 UTC  
> Updated at: 2024-05-23 21:28:59 UTC  
> Closed at: 2024-05-23 21:28:59 UTC  
> Url: https://github.com/boostorg/container/issues/280  

When allocators (like `std::allocator`) are always equal or implement `propagate_on_container_move_assignment` is true, according to the C++ standard a container supports types without a move constructor.  
  
Many Boost.Container containers don't support this because several checks are done in runtime instead of compile time, provoking the instantiation of the move assignment operation. Compile-time dispatching should be used when appropriate with allocator attributes to support such non-movable types in Boost.Container.
