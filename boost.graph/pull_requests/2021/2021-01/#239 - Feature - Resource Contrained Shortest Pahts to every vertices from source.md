# #239 Feature : Resource Contrained Shortest Pahts to every vertices from source [Open]

> Username: SimonPiCarter  
> Created at: 2021-01-08 21:56:26 UTC  
> Updated at: 2021-01-11 20:48:26 UTC  
> Url: https://github.com/boostorg/graph/pull/239  

See #238   
  
Closes #238   
  
If formatting or anything is to be changed let me know.

---

## Review 1 [Commented]

> Username: SimonPiCarter  
> Created_at: 2021-01-08 22:02:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/239#pullrequestreview-564612498  

📁 include/boost/graph/r_c_shortest_paths.hpp

```diff
 442 |+         else
 443 |+             // else only target
 444 |+             list_vertices.push_back(t);
```

> Username: SimonPiCarter  
> Created_at: 2021-01-08 21:58:28 UTC  
> Updated_at: 2021-01-10 18:45:37 UTC  
> Url: https://github.com/boostorg/graph/pull/239#discussion_r554217296  

Not satisfied with those lines but could not find a better way to implement this without impacting performance (very slightly) for the old usage.


📁 test/r_c_shortest_paths_to_all_test.cpp

```diff
   2 |+ // Distributed under the Boost Software License, Version 1.0.
   3 |+ // (See accompanying file LICENSE_1_0.txt or copy at
   4 |+ // http://boost.org/LICENSE_1_0.txt)
```

> Username: SimonPiCarter  
> Created_at: 2021-01-08 21:59:43 UTC  
> Updated_at: 2021-01-10 18:45:37 UTC  
> Url: https://github.com/boostorg/graph/pull/239#discussion_r554217784  

Copyright should be to be changed as the test is from me but I did not know the exact procedure for this.


---

## Comment 2

> Username: SimonPiCarter  
> Created_at: 2021-01-11 20:48:26 UTC  
> Url: https://github.com/boostorg/graph/pull/239#issuecomment-758213889  

builds seem to timeout on windows for mscv for unknown reason?

---
