# #178 Really overwrite previous value when using boost::fibers::fiber_speci… [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2018-07-16 22:06:50 UTC  
> Updated at: 2018-07-17 05:45:44 UTC  
> Merged at: 2018-07-17 05:45:39 UTC  
> Closed at: 2018-07-17 05:45:39 UTC  
> Url: https://github.com/boostorg/fiber/pull/178  

…fic_ptr::reset(newValue).  
  
Hi,  
  
Today, when a fss has a non null value, and you call reset() on it, the new value doesn't overwrite the old one. This is caused by the fact that we std::map::insert which will never overwrite an existing key. This trivial fix should repair this.  
  
Cheers,  
Romain

---

## Comment 1

> Username: olk  
> Created_at: 2018-07-17 05:45:44 UTC  
> Url: https://github.com/boostorg/fiber/pull/178#issuecomment-405466441  

ty

---
