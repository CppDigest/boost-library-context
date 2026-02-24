# #98 - Bug in `push_to_map_of_vec` template [Open]

> Username: niketagl  
> Created at: 2025-09-06 14:46:23 UTC  
> Updated at: 2025-09-06 14:46:23 UTC  
> Url: https://github.com/boostorg/msm/issues/98  

`push_to_map_of_vec` constructs a new fusion map from scratch using `make_vector` and `make_pair`, which only contains the single new key-value pair being added. ([ref](https://github.com/boostorg/msm/blob/develop/include/boost/msm/back11/dispatch_table.hpp#L202))  
  
This approach:  
- Discards all existing entries in the original map.  
- Only preserves the current key-value pair being processed.  
- Fails to maintain the accumulated state information across multiple operations.  
---  
Here’s how I think this can be fixed:  
- Convert the map to a vector representation  
- Remove the existing entry for the key (if present)  
- Create a new entry with the updated vector (original vector + new element)  
- Add the new entry back to the vector  
- Convert back to map format  
---  
The fix would maintain the same interface while correcting the fundamental logic error in map manipulation.  
I wanted to run it through you and get your feedback @henry-ch before raising a bugfix PR. Please let me know your thoughts on this!
