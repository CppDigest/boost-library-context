# #133 - Have you considered keeping the calculated hashes in the table entries? [Open]

> Username: hadrielk  
> Created at: 2022-06-27 11:46:13 UTC  
> Updated at: 2022-06-27 11:56:46 UTC  
> Url: https://github.com/boostorg/unordered/issues/133  

If I'm reading it right, it looks like the internal table is not storing the calculated hashes of the node keys. Instead, the hash is calculated on insertion to determine the bucket, and then thrown away. After that, only key-equality is used for things like `find()`, erasure, equality, etc.  
  
That's fine when the key is something small, like an `int`, but is slow when the key is _not_ small. Even for string type keys, if the container happens to have a lot of keys of the same string lengths, then equality checking is not cheap. And of course people use custom types for keys too, with potentially non-trivial equality comparisons.  
  
On the other hand, if you stored the full calculated hash in the nodes, you could skip past entries in the same bucket that don't have a matching full-hash, thereby improving performance. (at the expense of memory, obviously)  
  
Another example is rehashing when the load-factor is exceeded. Right now it looks like it has to re-calc the hash of every single node, whereas if it had stored the full-hash it wouldn't need to.  
  
LLVM/clang's `libc++` unordered containers store the full-hash, and gcc's `libstdc++` does as well in specific cases (e.g., for string-ish types, or if the user specifies the hashing is slow).  
  
So I'm wondering if you've considered doing that, or believe the memory tradeoff isn't worth it?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-06-27 11:52:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/133#issuecomment-1167256559  

We have considered it, repeatedly. It slows down integral keys, increases memory use, and doesn't pay off enough for string keys to be worth it. I like hash value caching, so I've suggested it more than once already, but our current thinking is that we shouldn't do it.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-06-27 11:56:45 UTC  
> Url: https://github.com/boostorg/unordered/issues/133#issuecomment-1167260376  

See https://github.com/boostorg/unordered/tree/feature/hash-caching
