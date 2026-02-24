# #523 - Any allocator, except buffer_allocator, inhibit move constructor of vector [Closed]

> Username: pisto  
> Created at: 2015-10-04 22:22:32 UTC  
> Updated at: 2015-11-04 21:21:36 UTC  
> Closed at: 2015-11-04 20:57:26 UTC  
> Url: https://github.com/boostorg/compute/issues/523  

A `boost::compute::vector` that does not use `boost::compute::buffer_allocator` (as for example `boost::compute::pinned_allocator`) can't use its move constructor. Inspect with a debugger the actual constructor that is called here:  
  
```  
compute::vector<int, compute::pinned_allocator<int>> a(100);  
compute::vector<int, compute::pinned_allocator<int>> b(move(a));   //copy constructor  
compute::vector<int> c(100);  
compute::vector<int> d(move(c));   //move constructor effectively called  
```  
  
I get this behavior with g++ 5.1.1. Actually ,gdb shows for the `b(move(a))` call the first line of the class when stepping into the constructor, which is usually what happens when the default synthesized copy constructor is called.

---

## Comment 1

> Username: pisto  
> Created at: 2015-10-04 22:57:55 UTC  
> Url: https://github.com/boostorg/compute/issues/523#issuecomment-145397333  

I found the problem. There are several places in vector.hpp where you declare other vector arguments as `vector<T>`. This is different from type `vector` within the class scope, because the `Alloc` template still takes the default value, which is in fact `buffer_allocator`. See this [ideone](https://ideone.com/fUfvNa). This means that any function with this mistake incurs in a copy through a default synthesized copy constructor, if an allocator different than `buffer_allocator` is used. I should say also that this is not just a performance problem (e.g. you get a copy construction but then everything runs fine), because I kept getting crashes, probably because of some misplaced `clRetain*` and `clRelease*` calls.  
  
I fear that I have seen this kind of mistake somewhere in the codebase, and a proper fixing will require some thought on how to handle operations between vectors with different allocators, so I leave patch crafting to you. For now, I use this for vector.hpp: https://gist.github.com/pisto/dd4f0dea133e03e618ba

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-10-05 12:10:43 UTC  
> Url: https://github.com/boostorg/compute/issues/523#issuecomment-145508316  

I confirm. PR in process.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-10-20 20:09:23 UTC  
> Url: https://github.com/boostorg/compute/issues/523#issuecomment-149687135  

I think it can be closed, see https://github.com/boostorg/compute/pull/525.

---

## Comment 4

> Username: pisto  
> Created at: 2015-11-04 20:57:26 UTC  
> Url: https://github.com/boostorg/compute/issues/523#issuecomment-153863100  

Should be fixed. Please also consider auditing copy construction or assignment, it crashed for some reason. Also there's no clear definition of what happens when moving/assigning between vectors with different allocators. The STL tends to have an overly complicate approach on these things imo, so I don't think it's a very good idea to follow it.

---

## Comment 5

> Username: jszuppe  
> Created at: 2015-11-04 21:18:24 UTC  
> Url: https://github.com/boostorg/compute/issues/523#issuecomment-153868352  

@pisto In current implementation (after merging https://github.com/boostorg/compute/pull/525) you can not use move operator between vectors with different allocators. Nut sure if it's needed. Assignment operator works correctly between vectors with different allocators.  
  
When did it crashed? Before https://github.com/boostorg/compute/pull/525 or after? I'm asking because I also kept getting crashes before https://github.com/boostorg/compute/pull/525 when using moving ctor with `Alloc =pinned_allocator<T>` , but none after.

---

## Comment 6

> Username: pisto  
> Created at: 2015-11-04 21:21:36 UTC  
> Url: https://github.com/boostorg/compute/issues/523#issuecomment-153869371  

Well the problem there was that the copy assignment was called instead of move. It also happened that the copy assignment made the program crash, probably because of a bad `clRetain()`/`clRelease()` sequence. That patch fixed selecting the move assignment, but I don't think it fixed copy assignment itself. Can't test now, but I don't see why it should work now.
