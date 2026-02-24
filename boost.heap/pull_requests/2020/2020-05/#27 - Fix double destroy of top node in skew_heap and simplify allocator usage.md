# #27 Fix double destroy of top node in skew_heap and simplify allocator usage [Merged]

> Username: glenfe  
> Created at: 2020-05-26 13:37:36 UTC  
> Updated at: 2020-05-28 13:17:38 UTC  
> Merged at: 2020-05-28 13:17:38 UTC  
> Closed at: 2020-05-28 13:17:38 UTC  
> Url: https://github.com/boostorg/heap/pull/27  



---

## Comment 1

> Username: glenfe  
> Created_at: 2020-05-27 13:59:24 UTC  
> Url: https://github.com/boostorg/heap/pull/27#issuecomment-634680177  

@timblechmann the changes are:  
* In the first commit:  
  * Fix the double-destroy of the `top` node in skew_heap (you can see the destructor being called and then called again).  
* In the second commit:  
  * For `allocate(n)` and `deallocate(p, n)` call them directly. Every allocator is required to provide them. Only `allocate(n, hint)` needs special handling (e.g. using `allocator_traits<A>::allocate(a, n, hint)` or `boost::allocator_allocate(a, n, hint)`.  
  * For construction and destruction because you're constructing nodes not `value_type`, use placement new. For something like `std::list<T>` you'll see that the node types are not constructed using the allocator, but instead the `T` inside the node is constructed using the allocator.  
  * Use the allocator access utilities instead of having `#ifdef BOOST_NO_CXX11_ALLOCATOR` blocks everywhere.

---

## Comment 2

> Username: timblechmann  
> Created_at: 2020-05-28 13:17:28 UTC  
> Url: https://github.com/boostorg/heap/pull/27#issuecomment-635344075  

looks all good, thanks a lot!

---
