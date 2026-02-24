# #247 Another hash_map implementation with peephole optimizations [Merged]

> Username: ldionne  
> Created at: 2016-01-28 21:52:49 UTC  
> Updated at: 2016-02-17 05:53:30 UTC  
> Merged at: 2016-02-16 21:09:50 UTC  
> Closed at: 2016-02-16 21:09:50 UTC  
> Url: https://github.com/boostorg/hana/pull/247  

I'm opening this PR so that we can discuss. This is related to #242 and #226. In fact, this is branched off of #242; I copied the design of the code and then optimized it iteratively.  
  
This is a rough work in progress only.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2016-01-29 00:51:55 UTC  
> Updated_at: 2016-01-29 00:52:15 UTC  
> Url: https://github.com/boostorg/hana/pull/247#issuecomment-176501286  

This is way cleaner/better than what I had.  
  
If we used something like `take_while` to get a run of elements whose keys couldn't possibly collide, we could construct a `hash_table` in one step and then use the `fold` approach when we encounter a run of one or more data types that might collide. I think 99.999% of the time it would take the whole sequence and make the `hash_table` in one step, significantly reducing that upfront cost.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-01-29 16:52:24 UTC  
> Url: https://github.com/boostorg/hana/pull/247#issuecomment-176857751  

> If we used something like take_while to get a run of elements whose keys couldn't possibly collide  
  
I don't understand how that might work? Indeed, if we were able to determine keys whose hashes won't collide, we could speed up the creation significantly. I just don't see how?

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2016-01-29 17:15:39 UTC  
> Updated_at: 2016-01-29 17:17:20 UTC  
> Url: https://github.com/boostorg/hana/pull/247#issuecomment-176868456  

I guess that it might create a problem with efficiently checking for dups, but if the first n-keys were `type`, `string`, or `int_c`, there is no way their hashes could collide without them also being equal.  
  
I'll see if I can get it to work on #242 just for a proof of concept.

---

## Comment 4

> Username: ricejasonf  
> Created_at: 2016-02-03 18:50:18 UTC  
> Url: https://github.com/boostorg/hana/pull/247#issuecomment-179400863  

Now that you are injecting the `HashTable`, perhaps it could be more general and call it something like `LookupTable`. We could then have a special `make_fast_map` function that makes a `map<TypeTable, Storage>` where `TypeTable` looks up elements strictly by the type of the hash. Actually, it could even still be a `detail::hash_table` but would be built in one step inside `make_fast_map`. Just an idea

---

## Comment 5

> Username: ldionne  
> Created_at: 2016-02-04 20:47:13 UTC  
> Url: https://github.com/boostorg/hana/pull/247#issuecomment-180042667  

Do you mean adding a user-facing function `make_fast_map` that can be used to create a `map` by providing one's own `HashTable` and `Storage`? If so, it seems like it would be exposing too much of the guts of the map implementation, making it impossible for us to change anything in the future without breaking the API.

---
