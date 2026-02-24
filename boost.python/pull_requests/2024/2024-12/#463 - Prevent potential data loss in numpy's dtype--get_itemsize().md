# #463 Prevent potential data loss in numpy's dtype::get_itemsize(). [Open]

> Username: JohannesWilde  
> Created at: 2024-12-14 14:49:13 UTC  
> Updated at: 2024-12-14 15:56:06 UTC  
> Url: https://github.com/boostorg/python/pull/463  

The numpy 2.0 migration guide states [https://numpy.org/devdocs/numpy_2_0_migration_guide.html]:  
  
"PyDataType_ELSIZE and PyDataType_SET_ELSIZE (note that the result is now npy_intp and not int)."  
  
Even though it is rather unlikely to have have elements larger than 2GB each, prevent potential numerical overflows, which could occur when casting ssize_t to int on 64-bit systems.

---

## Comment 1

> Username: JohannesWilde  
> Created_at: 2024-12-14 15:50:47 UTC  
> Updated_at: 2024-12-14 15:56:06 UTC  
> Url: https://github.com/boostorg/python/pull/463#issuecomment-2543163466  

As before, there is no check for numerical overflows in any of the multplications in `is_c_contiguous` and `is_f_contiguous` when doing `total *= (*i);`. Should someone change this?

---

## Comment 2

> Username: JohannesWilde  
> Created_at: 2024-12-14 15:52:17 UTC  
> Url: https://github.com/boostorg/python/pull/463#issuecomment-2543163914  

The file `ndarray.cpp` still contains several tab characters. Do you have coding rules relating to tabs?

---
