# #424 examples: C++11: Use std::begin() and std::end() [Merged]

> Username: murraycu  
> Created at: 2025-02-16 15:00:40 UTC  
> Updated at: 2025-03-09 23:11:57 UTC  
> Merged at: 2025-03-09 23:11:57 UTC  
> Closed at: 2025-03-09 23:11:57 UTC  
> Url: https://github.com/boostorg/graph/pull/424  

Instead of array + length.  
And use initializer lists.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-02-20 09:32:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/424#pullrequestreview-2629202923  

📁 example/bellman-ford-internet.cpp

```diff
  36 |     // Specify the graph type and declare a graph object
  37 |-     typedef edge_list< Edge*, Edge, std::ptrdiff_t,
  37 |+     typedef edge_list< const Edge*, Edge, std::ptrdiff_t,
```

> Username: jeremy-murphy  
> Created_at: 2025-02-20 09:24:54 UTC  
> Updated_at: 2025-02-20 09:32:01 UTC  
> Url: https://github.com/boostorg/graph/pull/424#discussion_r1963161618  

I'm not sure that constant data is a good way to demonstrate the library, just because being able to change edges and mutate graphs is such a normal practice. If users learn to create const data to start with, I expect they'll get frustrated as they try to do things with it until they eventually realise that the const was what was holding them back.   
This is very much an argument based on psychology rather than technical merit or const-correctness. :)

> Username: murraycu  
> Created_at: 2025-03-09 17:50:56 UTC  
> Updated_at: 2025-03-09 17:50:57 UTC  
> Url: https://github.com/boostorg/graph/pull/424#discussion_r1986380403  

OK. Done.


---
