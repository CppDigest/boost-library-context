# #609 Enable copying with copy buffer op for device_ptr [Merged]

> Username: jszuppe  
> Created at: 2016-05-03 20:04:57 UTC  
> Updated at: 2016-05-04 06:57:03 UTC  
> Merged at: 2016-05-04 03:58:09 UTC  
> Closed at: 2016-05-04 03:58:09 UTC  
> Url: https://github.com/boostorg/compute/pull/609  

This enables copying data using `clEnqueueCopyBuffer()` in `boost::compute::copy()` algorithm when user passes `device_ptr<>`/s.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-05-03 21:50:56 UTC  
> Updated_at: 2016-05-03 22:53:51 UTC  
> Url: https://github.com/boostorg/compute/pull/609#issuecomment-216675668  

[![Coverage Status](https://coveralls.io/builds/6035856/badge)](https://coveralls.io/builds/6035856)  
  
Coverage increased (+0.02%) to 88.873% when pulling **02e12b40fde9189acc358586e95963fff67fee61 on haahh:pr_copy_buffer_for_device_ptr** into **51afcd0c22e85c4f764de77fcdbd1ee834c02025 on boostorg:develop**.

---
