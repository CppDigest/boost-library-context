# #132 Fix warning about assigned but unused variable num_elements [Merged]

> Username: mloskot  
> Created at: 2018-08-25 20:56:19 UTC  
> Updated at: 2018-08-27 07:12:10 UTC  
> Merged at: 2018-08-27 07:12:07 UTC  
> Closed at: 2018-08-27 07:12:07 UTC  
> Url: https://github.com/boostorg/gil/pull/132  

The `num_elements` is used only in `assert`, so it is unused in optimised builds.

---
