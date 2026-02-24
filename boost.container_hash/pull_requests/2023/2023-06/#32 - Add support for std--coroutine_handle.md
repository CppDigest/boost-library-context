# #32 Add support for std::coroutine_handle [Open]

> Username: cmazakas  
> Created at: 2023-06-07 18:21:13 UTC  
> Updated at: 2025-04-08 18:02:39 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32  

We need to keep with the times  
  
https://godbolt.org/z/1andznGhq

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-06-07 18:42:57 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-1581333193  

What's the intended use of `boost::hash<coroutine_handle>`?

---

## Comment 2

> Username: cmazakas  
> Created_at: 2023-06-07 18:49:44 UTC  
> Updated_at: 2023-06-07 18:50:12 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-1581341576  

> What's the intended use of `boost::hash<coroutine_handle>`?  
  
In my case, it's for storing an `unordered_flat_set<coroutine_handle>` for use as an io_uring runtime. To this end, I can store and lookup tasks as required.  
  
The other motivation is just a simple: "Well, the stdlib does it so we should too"

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-06-07 18:54:35 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-1581347341  

Aren't you going to store `task`s in the unordered set, though, instead of the raw coroutine_handle? In which case you'll need to specialize `boost::hash<task>` anyway.

---

## Comment 4

> Username: cmazakas  
> Created_at: 2023-06-07 20:28:02 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-1581463133  

> Aren't you going to store `task`s in the unordered set, though, instead of the raw coroutine_handle? In which case you'll need to specialize `boost::hash<task>` anyway.  
  
This is a good question and I don't necessarily have a great response to it.  
  
I might wind up storing `coroutine_handle<>` which is type-erased on the `promise_type`. This way, the runtime can store arbitrary tasks so long as everything it needs is kept in the promise type or coroutine frame itself. But I'm hesitant to push for this as I have no idea at this stage if this is viable or not.  
  
I can, instead, do some external market research and see how other people are using `hash<coroutine_handle<T>>`.

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-10-12 17:55:55 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-1760098108  

The test is overspecified; it should test that the hashes of t and t2 match and that they don't match the hash of t3 (a second task).  
  
We don't want to guarantee that hashing a coroutine_handle produces the same result as hashing .address, because we want to be able to return the result of `std::hash<std::coroutine_handle>`.  
  
If we want to be stricter than that and check that the hash isn't of extremely poor quality, we can do a collision test with something like 256 values, checking that there are no duplicates, as is done f.ex. in https://github.com/boostorg/container_hash/blob/develop/test/hash_number_test2.cpp.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-10-17 15:08:15 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-1766615906  

On the other hand, for transparent hashing purposes, we do want to guarantee that `boost::hash<std::coroutine_handle>()( h ) == boost::hash<void*>()( h.address() )`, and if `std::hash<std::coroutine_handle>` is used, this won't hold. :-/

---

## Comment 7

> Username: cmazakas  
> Created_at: 2025-04-08 18:02:38 UTC  
> Url: https://github.com/boostorg/container_hash/pull/32#issuecomment-2787263777  

@pdimov should we revisit this?

---
