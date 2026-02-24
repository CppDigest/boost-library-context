# #40 add xxh3-128 implementation [Merged]

> Username: cmazakas  
> Created at: 2025-04-22 19:26:17 UTC  
> Updated at: 2025-04-30 20:53:54 UTC  
> Merged at: 2025-04-30 19:30:38 UTC  
> Closed at: 2025-04-30 19:30:38 UTC  
> Url: https://github.com/boostorg/hash2/pull/40  

I had trouble with some of the tests.  
  
By definition, xxh3-128 is stateless for the empy string so I wasn't sure how to satisfy the requirement that `h.result(); h.result();` yields different values.

---
