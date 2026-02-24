# #17 add sha-2 reference docs [Merged]

> Username: cmazakas  
> Created at: 2024-10-18 22:49:15 UTC  
> Updated at: 2024-10-24 20:21:27 UTC  
> Merged at: 2024-10-24 12:12:38 UTC  
> Closed at: 2024-10-24 12:12:38 UTC  
> Url: https://github.com/boostorg/hash2/pull/17  

very rough draft of reference docs for the sha-2 family  
  
let me know what you'd like to see here.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-10-18 23:05:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/17#pullrequestreview-2379066536  

📁 doc/hash2/sha-2.adoc

```diff
   4 |+ 
   5 |+ ```cpp
   6 |+ #include <boost/hash2/sha2.hpp>
```

> Username: pdimov  
> Created_at: 2024-10-18 23:05:43 UTC  
> Updated_at: 2024-10-18 23:05:44 UTC  
> Url: https://github.com/boostorg/hash2/pull/17#discussion_r1807083260  

`sha2.hpp` doesn't include itself.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2024-10-18 23:11:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/17#pullrequestreview-2379069201  

📁 doc/hash2/sha-2.adoc

```diff
   1 |+ ### <boost/hash2/sha2.hpp>
```

> Username: pdimov  
> Created_at: 2024-10-18 23:11:05 UTC  
> Url: https://github.com/boostorg/hash2/pull/17#discussion_r1807085168  

Heading level should start from one, and be adjusted with leveloffset in the include. The .adoc should probably be sha2.adoc (like sha2.hpp), not sha-2.adoc.


---
