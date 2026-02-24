# #733 Implement vector::shrink_to_fit() [Merged]

> Username: jszuppe  
> Created at: 2017-06-17 15:53:09 UTC  
> Updated at: 2017-07-10 21:49:52 UTC  
> Merged at: 2017-07-10 21:49:52 UTC  
> Closed at: 2017-07-10 21:49:52 UTC  
> Url: https://github.com/boostorg/compute/pull/733  

This pull-request:  
  
* fixes warning referenced in #730,  
* implements `boost::compute::vector::shrink_to_fit()` (which resolves #732), and  
* fixes memory management issue in `boost::compute::vector`: memory deallocation should depend on `vector`'s capacity, not on its `m_size`.

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-06-17 17:13:39 UTC  
> Url: https://github.com/boostorg/compute/pull/733#issuecomment-309227869  

[![Coverage Status](https://:/builds/12016354/badge)](https://:/builds/12016354)  
  
Coverage increased (+0.06%) to 83.739% when pulling **6c2a3604b948f40edc1ba34d396652bcedcc0062 on jszuppe:pr_vector_shrink_to_fit** into **251d02b17dd75fa772964a01aebae139f87f057a on boostorg:develop**.

---
