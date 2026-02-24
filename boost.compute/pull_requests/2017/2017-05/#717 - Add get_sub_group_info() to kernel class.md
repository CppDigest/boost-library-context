# #717 Add get_sub_group_info() to kernel class [Merged]

> Username: jszuppe  
> Created at: 2017-05-07 10:24:50 UTC  
> Updated at: 2017-05-10 11:37:58 UTC  
> Merged at: 2017-05-10 11:37:58 UTC  
> Closed at: 2017-05-10 11:37:58 UTC  
> Url: https://github.com/boostorg/compute/pull/717  



---

## Comment 1

> Username: coveralls  
> Created_at: 2017-05-07 11:38:52 UTC  
> Url: https://github.com/boostorg/compute/pull/717#issuecomment-299700277  

[![Coverage Status](https://coveralls.io/builds/11407725/badge)](https://coveralls.io/builds/11407725)  
  
Coverage remained the same at 83.676% when pulling **aa778dc8221f074136003505a70238ff510b129d on jszuppe:pr_subgroup_info** into **a965a8dab6fe0b997bf3a17c298653a34d7c1714 on boostorg:develop**.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2017-05-09 19:00:31 UTC  
> Url: https://github.com/boostorg/compute/pull/717#issuecomment-300268506  

* Add `get_sub_group_info()` method  
* Use latest OpenCL headers for AppVeyor CI  
* Various other small fixes.  
  
`get_sub_group_info()` method returns `boost::optional<T>`. That way, instead of throwing exception in user's face, we can just return null optional if device is not 2.1 device, or is not 2.0 device with support for `cl_khr_subgroups` extension.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2017-05-09 20:34:55 UTC  
> Url: https://github.com/boostorg/compute/pull/717#issuecomment-300293163  

Still no support for AppVeyor in Github settings, so https://ci.appveyor.com/project/jszuppe/compute-e97yn.

---
