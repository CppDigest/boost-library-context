# #896 - Default LRU cache constructor is invalid [Open]

> Username: rekrutik  
> Created at: 2025-01-31 12:53:51 UTC  
> Updated at: 2025-01-31 12:54:29 UTC  
> Url: https://github.com/boostorg/compute/issues/896  

The copy constructor of boost::compute::detail::lru_cache does not seem to work properly: the new map holds invalidated std::list iterators after its copying. I believe that the copy constructor should be deleted there at all (seems reasonable for this class), or iterators should be restored from the new list.

---

## Comment 1

> Username: rekrutik  
> Created at: 2025-01-31 12:54:28 UTC  
> Url: https://github.com/boostorg/compute/issues/896#issuecomment-2627250523  

#895 fixes this.
