# #731 Add wrapper for clEnqueueSVMMigrateMem() [Merged]

> Username: jszuppe  
> Created at: 2017-06-10 15:26:04 UTC  
> Updated at: 2017-06-11 20:20:49 UTC  
> Merged at: 2017-06-11 20:18:48 UTC  
> Closed at: 2017-06-11 20:18:48 UTC  
> Url: https://github.com/boostorg/compute/pull/731  



---

## Comment 1

> Username: coveralls  
> Created_at: 2017-06-10 17:28:27 UTC  
> Url: https://github.com/boostorg/compute/pull/731#issuecomment-307579110  

[![Coverage Status](https://coveralls.io/builds/11917000/badge)](https://coveralls.io/builds/11917000)  
  
Coverage remained the same at 83.684% when pulling **baa3e36fade6aaf0b4647970188d7f7dd6dc150e on jszuppe:pr_svm_migrate_mem** into **ed2fadbac6c0897171861d64ada9ab23dd45f0b6 on boostorg:develop**.

---

## Review 2 [Commented]

> Username: kylelutz  
> Created_at: 2017-06-11 02:17:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/731#pullrequestreview-43309991  

📁 include/boost/compute/command_queue.hpp

```diff
1831 |+     /// \see_opencl21_ref{clEnqueueSVMMigrateMem}
1832 |+     event enqueue_svm_migrate_memory(const std::vector<const void*> svm_ptrs,
1833 |+                                      const std::vector<size_t> sizes,
```

> Username: kylelutz  
> Created_at: 2017-06-11 02:17:47 UTC  
> Updated_at: 2017-06-11 06:19:52 UTC  
> Url: https://github.com/boostorg/compute/pull/731#discussion_r121270517  

Should these `std::vector` args be const-references? No need to make an extra copy.

> Username: jszuppe  
> Created_at: 2017-06-11 06:20:35 UTC  
> Url: https://github.com/boostorg/compute/pull/731#discussion_r121272952  

Thanks. Fixed.


---

## Comment 3

> Username: coveralls  
> Created_at: 2017-06-11 07:35:33 UTC  
> Url: https://github.com/boostorg/compute/pull/731#issuecomment-307612119  

[![Coverage Status](https://coveralls.io/builds/11919997/badge)](https://coveralls.io/builds/11919997)  
  
Coverage remained the same at 83.684% when pulling **f568c447e392dbbccfd41d2c522d78796ae180fa on jszuppe:pr_svm_migrate_mem** into **08e00e3848d7cc62d39ef39cd9d3caa0d0c50ff9 on boostorg:develop**.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2017-06-11 20:18:49 UTC  
> Url: https://github.com/boostorg/compute/pull/731#issuecomment-307654066  

Looks good, merged!

---
