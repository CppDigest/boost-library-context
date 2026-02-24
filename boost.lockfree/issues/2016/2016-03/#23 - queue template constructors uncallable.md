# #23 - queue template constructors uncallable [Open]

> Username: tamboril  
> Created at: 2016-03-29 12:51:32 UTC  
> Updated at: 2019-04-16 06:38:27 UTC  
> Url: https://github.com/boostorg/lockfree/issues/23  

Using queue with an allocator, it is impossible to write code that invokes this template constructor:  
  
```  
template <typename U>  
    queue(size_type n, typename node_allocator::template rebind<U>::other const & alloc):  
```  
  
(or the other one with the rebind).  
  
The template argument U cannot be deduced (at least with gcc 5.3.0).  
  
I had to add the following form in order to use this with a custom allocator:  
  
`explicit queue(size_type n, allocator const & alloc)`  
  
I notice that the constructor that takes only an `alloc` already has this overload.

---

## Comment 1

> Username: star-e  
> Created at: 2019-04-16 06:38:27 UTC  
> Url: https://github.com/boostorg/lockfree/issues/23#issuecomment-483530838  

This problem is still not fixed in boost 1.69, with msvc2019 v142.  
Using custom allocator with lockfree::fixed_sized<true> is impossible.
