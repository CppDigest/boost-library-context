# #242 - Is erase safe for unordered concurrent structures [Closed]

> Username: diehard2  
> Created at: 2024-04-29 16:10:43 UTC  
> Updated at: 2024-04-30 07:45:15 UTC  
> Closed at: 2024-04-30 07:45:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/242  

Apologies for a question under issues, but I couldn't find a better place for this. Most (all?) concurrent containers are unsafe on erase. They appear to usually be marked with as 'unsafe_erase'. I can't find any documentation on the boost concurrent container's safety on the erase operation. Is it safe to erase without a lock?

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-04-30 07:45:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/242#issuecomment-2084613398  

Yes, `erase` is safe, this is mentioned [here](https://www.boost.org/doc/libs/1_85_0/libs/unordered/doc/html/unordered.html#concurrent_flat_map) (bold mine):  
  
> `boost::concurrent_flat_map` — A hash table that associates unique keys with another value and allows for concurrent element insertion, **erasure**, lookup and access without external synchronization mechanisms.
