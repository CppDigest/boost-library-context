# #32 - Performance issue on resize [Open]

> Username: schaumb  
> Created at: 2022-08-13 07:03:17 UTC  
> Updated at: 2022-08-13 13:59:21 UTC  
> Url: https://github.com/boostorg/multi_array/issues/32  

When I use `boost::multi_array` with `boost::fortran_storage_order()`, and I want to `.resize` by only the latest (rightmost) dimension, the current implementation is a nested `N` function call (`std::copy`), one by dimension.  
The  same functionality can be reached with one `std::copy` on the storage.  
  
This minimal example shows the problem:  
https://godbolt.org/z/GWbxcM14h
