# #45 Make intrusive_ptr move constructible/assignable  [Merged]

> Username: stheophil  
> Created at: 2017-12-04 16:43:08 UTC  
> Updated at: 2017-12-08 09:45:51 UTC  
> Merged at: 2017-12-08 09:40:22 UTC  
> Closed at: 2017-12-08 09:40:22 UTC  
> Url: https://github.com/boostorg/interprocess/pull/45  

… and made its methods noexcept. I hope the BOOST_NOEXCEPT macro is correct for backwards compatibility.  
  
Also added reset() method in line with std::shared_ptr::reset / unique_ptr::reset. In several places of our code I've found this addition useful because the caller had to lock the shared_heap before releasing a reference, so just letting ~intrusive_ptr release the ptr was not correct.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-12-08 09:41:52 UTC  
> Url: https://github.com/boostorg/interprocess/pull/45#issuecomment-350219295  

Many thanks for your patch ! After committing, I've updated it to support Boost.Move and older compilers but it should be equivalent to your patch.

---

## Comment 2

> Username: stheophil  
> Created_at: 2017-12-08 09:45:51 UTC  
> Url: https://github.com/boostorg/interprocess/pull/45#issuecomment-350220177  

You're welcome!

---
