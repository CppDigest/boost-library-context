# #35 - Build d_ary_heap from vector [Open]

> Username: mglisse  
> Created at: 2023-07-10 07:52:03 UTC  
> Updated at: 2023-07-10 07:52:03 UTC  
> Url: https://github.com/boostorg/heap/issues/35  

`d_ary_heap` has an interface similar to `std::priority_queue`, but even more limited because it gives no access to the underlying container. I would find it useful to have a bit more flexibility. In particular, I would like a way to turn a vector into a heap. This isn't such an odd use case, while processing something I append elements to a vector, and if some criterion is satisfied, I turn it into a heap. Using a heap from the start would be wasteful: if the criterion is not satisfied, I won't need the heap property. And even if it is, I only need the heap property after some point, and `make_heap` can be more efficient than a sequence of `push`.  
  
Without going all the way to providing `*_heap`-like functions as in the standard binary heap (though that would be great), one possibility would be a constructor `d_ary_heap(std::vector&&)` which adopts the content of the input vector and turns it efficiently into a heap (calling decrease on the beginning of the vector backwards?), possibly with some extra tag argument.  
  
Also, at some point in the processing, I sometimes realize that I am going to need the whole array sorted. An in-place `sort_heap` and a way to access the resulting underlying vector would help. Again, if you don't want to provide functions the user can call manually on a vector, and if you don't want to break the invariant of the heap data structure, we could imagine something like `std::vector sort()&&{...}` which does the sorting but gives away its buffer and results in an empty heap.  
  
Of course, the main drawback is that it forces to document the underlying container and makes it harder to change it in later releases. But that doesn't seem likely to happen anyway. Also, remember that performance is the main selling point of this library.
