# #866 - Unwanted function call for std::map::operator[] [Open]

> Username: ankurhero  
> Created at: 2021-06-30 10:31:19 UTC  
> Updated at: 2022-08-30 18:40:00 UTC  
> Url: https://github.com/boostorg/compute/issues/866  

https://github.com/boostorg/compute/blob/36c89134d4013b2e5e45bc55656a18bd6141995a/include/boost/compute/detail/lru_cache.hpp#L102  
  
  
Since i is a valid iterator, we can replace this call with.  
**m_map[key]** = std::make_pair(value, j);  
  
with  
  
**i->second** = std::make_pair(value, j);  
  
avoiding the second map search.

---

## Comment 1

> Username: chetanpandey1266  
> Created at: 2022-08-30 18:40:00 UTC  
> Url: https://github.com/boostorg/compute/issues/866#issuecomment-1232027432  

I don't think this will work as iterator is not passing to the location corresponding to `key` but to a random position ( `map.end()` ).
