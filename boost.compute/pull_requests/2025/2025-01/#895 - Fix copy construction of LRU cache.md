# #895 Fix copy construction of LRU cache [Open]

> Username: rekrutik  
> Created at: 2025-01-20 20:10:04 UTC  
> Updated at: 2025-01-20 20:17:08 UTC  
> Url: https://github.com/boostorg/compute/pull/895  

The copy constructor of `boost::compute::detail::lru_cache` does not seem to work properly: the new map holds invalidated `std::list` iterators after its copying. I believe that the copy constructor should be deleted there at all (seems reasonable for this class), or iterators should be restored from the new list, that's done in this PR.

---
