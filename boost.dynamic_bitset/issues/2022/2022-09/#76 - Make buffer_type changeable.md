# #76 - Make buffer_type changeable [Closed]

> Username: schaumb  
> Created at: 2022-09-20 19:19:48 UTC  
> Updated at: 2025-09-10 13:53:20 UTC  
> Closed at: 2025-09-10 13:53:20 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/76  

I want to use dynamic_bitset with boost::container::small_vector, but currently it is not possible because buffer type is not changeable.  
  
Other adapter containers like boost::container::flat_set allows to pass an "allocator or container" template argument.  
This modification can be made for dynamic_bitset too in a backward compatible way.

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-08-01 08:18:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/76#issuecomment-3143685783  

Yes, but what containers could actually be used other than `std::vector` and `boost::container::small_vector`? `dynamic_bitset` needs one that has `reserve()` and `capacity()`.

---

## Comment 2

> Username: schaumb  
> Created at: 2025-08-01 08:28:22 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/76#issuecomment-3143711173  

While my primary use case is to use dynamic_bitset with boost::container::small_vector, I can imagine other users might explore using it with alternative container types, such as std::basic_string<> or even custom vector-like implementations. As long as the container satisfies the required interface—specifically supporting reserve() and capacity()—it could be a valid candidate.

---

## Comment 3

> Username: gennaroprota  
> Created at: 2025-08-01 10:35:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/76#issuecomment-3144101669  

But my point is: There would be, de facto, very few candidates. So, is this support worth adding? It seems a case of "it could", but never is, in practice.

---

## Comment 4

> Username: gennaroprota  
> Created at: 2025-09-09 14:40:36 UTC  
> Updated at: 2025-09-09 14:50:01 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/76#issuecomment-3271047441  

Change of course :-). There was a request on the developers list to implement small buffer optimization (SBO). That makes sense, as many (most?) `dynamic_bitset`s are small. So, rather than implementing SBO directly, I opted to make `buffer_type` customizable. This way, one can plug in `boost::container::small_vector` to get SBO, just as you wanted.
