# #387 - Rename enum `convolve_boundary_option` and move to saperate file [Closed]

> Username: lpranam  
> Created at: 2019-08-31 13:39:40 UTC  
> Updated at: 2020-02-22 13:13:53 UTC  
> Closed at: 2019-09-18 09:48:20 UTC  
> Url: https://github.com/boostorg/gil/issues/387  

In recent time with GSoC project, new algorithms are introduced which uses this enum to provide boundary options (i.e: `adaptive_threshold`). I propose this to rename to more general name like `boundary_option` and moved to a separate file so any algorithm can use it. I realised the need of this with my previous PR #386.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-08-31 14:09:43 UTC  
> Url: https://github.com/boostorg/gil/issues/387#issuecomment-526833584  

If the enum is usable in more places, it's certainly a good idea to move it to more common header.  
  
If the set of enumerators are usable in other algorithms not just convolve, then renaming it with more generic name makes sense too.  
  
Feel free to propose PR

---

## Comment 2

> Username: mloskot  
> Created at: 2019-09-09 07:45:43 UTC  
> Url: https://github.com/boostorg/gil/issues/387#issuecomment-529340641  

AFAIR, @stefanseefeld is planning to merge Numeric into core features of the library. I've nudge Stefan about the status of this idea https://lists.boost.org/boost-gil/2019/09/index.php It is likely that the extension move will affect this idea here.
