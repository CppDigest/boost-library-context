# #4 add sha-224 impl [Merged]

> Username: cmazakas  
> Created at: 2024-09-26 22:48:35 UTC  
> Updated at: 2024-09-30 14:45:56 UTC  
> Merged at: 2024-09-26 23:11:38 UTC  
> Closed at: 2024-09-26 23:11:38 UTC  
> Url: https://github.com/boostorg/hash2/pull/4  

This is a small prototype/idea of how to factor some of the common functionality into the same place.  
  
Maybe the base can become templated on NTTPs and we can use this for the sha-512 variants.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-09-26 23:14:29 UTC  
> Url: https://github.com/boostorg/hash2/pull/4#issuecomment-2378103274  

> Maybe the base can become templated on NTTPs and we can use this for the sha-512 variants.  
  
As a first pass, just use `sha2_512_base` there as well. Then maybe refactor the two bases in a separate PR if it turns out to be a simplification.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-09-30 14:45:54 UTC  
> Url: https://github.com/boostorg/hash2/pull/4#issuecomment-2383416673  

You haven't added sha256 and sha224 to the tests and benchmarks (as done [here](https://github.com/pdimov/hash2/commit/efb81661521c44b4e2e3b5ee21d3422a7bdf7993)).

---
