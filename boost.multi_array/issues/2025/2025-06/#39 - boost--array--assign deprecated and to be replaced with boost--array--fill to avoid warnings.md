# #39 - boost::array::assign deprecated and to be replaced with boost::array::fill to avoid warnings [Closed]

> Username: GreenGary  
> Created at: 2025-06-23 13:17:56 UTC  
> Updated at: 2026-02-06 01:38:17 UTC  
> Closed at: 2026-02-06 01:38:17 UTC  
> Url: https://github.com/boostorg/multi_array/issues/39  

`boost::array::assign` is now deprecated and shall be replaced with `boost::array::fill`. Otherwise, compiler will issue warnings:  
  
```boost/multi_array/view.hpp(234): warning C4996: 'boost::array<__int64,5>::assign': please use `fill` instead```  
  
[multi_array-view-assign-to-fill.patch](https://github.com/user-attachments/files/20865896/multi_array-view-assign-to-fill.patch)  
  
  
[multi_array-multi_array_ref-assign-to-fill.patch](https://github.com/user-attachments/files/20975791/multi_array-multi_array_ref-assign-to-fill.patch)  
  
[multi_array-storage_order-assign-to-fill.patch](https://github.com/user-attachments/files/20975794/multi_array-storage_order-assign-to-fill.patch)  
  
boost 1.85 was OK, boost 1.88 is affected.  
  
  
I can open a MR with attached patch file.

---

## Comment 1

> Username: brandl-muc  
> Created at: 2026-01-27 08:02:19 UTC  
> Url: https://github.com/boostorg/multi_array/issues/39#issuecomment-3803686501  

Sadly, this is still present on develop. Any change this can get some attention? The supplied PR looks good.
