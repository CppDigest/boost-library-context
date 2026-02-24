# #54 Heterogeneous erase [Merged]

> Username: cmazakas  
> Created at: 2021-12-03 18:20:41 UTC  
> Updated at: 2021-12-28 16:18:27 UTC  
> Merged at: 2021-12-06 20:10:07 UTC  
> Closed at: 2021-12-06 20:10:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/54  

Closes #46

---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-12-03 19:22:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/54#issuecomment-985769564  

@pdimov This PR implements heterogeneous `erase()`.  
  
Note, this one took a little more refactoring than the others.  
  
We needed ways of probing the forward list for the `prev` node in order to do the erasing like it's done now so I added an `_impl` variant that is generic over the binary predicate and the key type to test against.  
  
I didn't remove `erase_key_unique()` yet because it's depended on by `unordered_set` too.

---
