# #358 Improves flat_tree implementation [Merged]

> Username: anarthal  
> Created at: 2025-11-20 17:52:03 UTC  
> Updated at: 2025-11-29 20:35:59 UTC  
> Merged at: 2025-11-29 20:35:54 UTC  
> Closed at: 2025-11-29 20:35:54 UTC  
> Url: https://github.com/boostorg/redis/pull/358  

* Makes flat_tree implementation use a custom buffer. This allows:  
  * Never dangling nodes (previously, node values could dangle after calling reserve() or if notify_done() wasn't called).  
  * Reduced memory consumption  
  * Increased runtime speed  
* Changes flat_tree assignment to the usual signature and semantics  
* Fixes a bug causing an assertion to trigger when copy-constructing an empty flat_tree.  
* Changes basic_node operator== and operator!= return type   
* Adds generic_flat_response, basic_tree, tree, view_tree, flat_tree to the reference page.  
* Adds a missing resp3:: qualifier to all names in the reference page that belong to the resp3 namespace.  
* Adds reference documentation to flat_tree.  
* Mentions generic_flat_response in the discussion.  
* Adds operator!= for basic_node to basic_node's reference page.  
* Adds test_flat_tree.  
  
close #357   
close #354   
close #352

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-11-20 17:58:17 UTC  
> Url: https://github.com/boostorg/redis/pull/358#issuecomment-3559343748  

@mzimbres I've written a new implementation for `flat_tree` that should fix the problems I reported. I've used a home-grown buffer like the one I shown in the comparison I did [here](https://github.com/boostorg/redis/pull/340#issuecomment-3451362531). The idea is:  
  
* We have just a `view_tree`, no offsets.  
* Since we control the buffer, we can hook up to the reallocation process.  
* When a reallocation happens, we compute the offsets by doing pointer arithmetic and update the strings. Strings are guaranteed to be valid at any point (so we have stronger invariants). The only point where doing this arithmetic is not UB is right after the new buffer is allocated and before the old one is deleted.  
  
Before I start writing the tests and docs, could you please have a look and double check if you think this is a good approach?

---
