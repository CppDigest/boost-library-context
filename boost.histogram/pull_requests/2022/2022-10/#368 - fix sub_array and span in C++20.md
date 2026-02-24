# #368 fix sub_array and span in C++20 [Merged]

> Username: HDembinski  
> Created at: 2022-10-02 12:49:27 UTC  
> Updated at: 2022-10-03 11:57:09 UTC  
> Merged at: 2022-10-03 11:57:09 UTC  
> Closed at: 2022-10-03 11:57:09 UTC  
> Url: https://github.com/boostorg/histogram/pull/368  

- std::swap applied to sub_array also swaps size  
- swap_element_is_noexcept must be static  
- operator== on sub_array now also compares sizes  
- don't switch to std::span if it is available, because it does not recognize sub_array as a container

---
