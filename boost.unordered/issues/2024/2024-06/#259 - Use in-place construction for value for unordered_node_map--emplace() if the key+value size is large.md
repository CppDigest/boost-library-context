# #259 - Use in-place construction for value for unordered_node_map::emplace() if the key+value size is large? [Closed]

> Username: user202729  
> Created at: 2024-06-26 07:49:10 UTC  
> Updated at: 2024-06-26 22:53:43 UTC  
> Closed at: 2024-06-26 22:53:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/259  

As explained in https://github.com/boostorg/unordered/issues/241 , if the object is movable, the current implementation of emplace() first construct the key-value pair on the stack, then move it to the heap if the key did not exist.  
  
This usually improve the performance, but it's not always the case.  
  
| `sizeof(key)+sizeof(value)` ↓ \\ Key already exists → | often | rare |  
|--|--|--|  
| small | `move()` is better (saves a new/delete pair) | about the same |  
| large | `move()` is negligibly better (cost of new/delete is negligible compared to actually constructing the pair) | worse! (`move()` is expensive) |  
  
Suggestion: if `sizeof(key)+sizeof(value)` is larger than a certain threshold (say, 60 bytes — often, at this rate the compiler's optimizer give up and the key/value actually touch the memory instead of staying in registers? Or let the user configure this?) then switch to always allocate on the heap.  
  
What do you think?

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-06-26 17:55:54 UTC  
> Url: https://github.com/boostorg/unordered/issues/259#issuecomment-2192308588  

Hi, beginning with [Boost 1.85](https://www.boost.org/libs/unordered/doc/html/unordered.html#changes_release_1_85_0), Boost.Unordered has an optimization by which `emplace(key, value)` does not construct any intermediate object when the type of `key` is  `key_type` (or a const reference to that etc.) Does this address your issue?
