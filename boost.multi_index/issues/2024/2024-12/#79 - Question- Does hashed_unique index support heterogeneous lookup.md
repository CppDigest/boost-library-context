# #79 - Question: Does hashed_unique index support heterogeneous lookup [Closed]

> Username: void-mian  
> Created at: 2024-12-18 07:35:47 UTC  
> Updated at: 2024-12-18 14:21:52 UTC  
> Closed at: 2024-12-18 14:21:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/79  

The STL support heterogeneous lookup in c++20 (example: https://en.cppreference.com/w/cpp/container/unordered_map/find). And after reading through multi_index::hashed_unique's find implement, I feel that multi_index already support it natively?

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-12-18 11:57:19 UTC  
> Updated at: 2024-12-18 11:57:33 UTC  
> Url: https://github.com/boostorg/multi_index/issues/79#issuecomment-2551135579  

Yes, heterengeous lookup is supported in hashed (and ordered) indices:  
  
https://www.boost.org/libs/multi_index/doc/reference/hash_indices.html#lookup  
  
There are differences with respect to how this is supported in the standard:  
  
* There's no need to mark the hash function and equality predicate as `is_transparent`: they are always assumed to be so.  
* Additional lookup functions are provided where transparent hash and equality objects are passed to be used instead of those of the index proper. This is an extension with respect to the standard.

---

## Comment 2

> Username: void-mian  
> Created at: 2024-12-18 14:21:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/79#issuecomment-2551448013  

Thanks for your patient and detailed answer.
