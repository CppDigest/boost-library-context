# #573 - std::has_denorm/std::has_denorm_loss are deprecated in c++23 [Closed]

> Username: wanghan02  
> Created at: 2023-12-05 10:54:09 UTC  
> Updated at: 2023-12-08 11:28:25 UTC  
> Closed at: 2023-12-08 11:28:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/573  

`std::has_denorm`/`std::has_denorm_loss` are deprected in c++23. Latest VC++ 19.38 gives warning about this with `/std:c++latest /W4` where we specialize `std::numeric_limits` in `boost::multiprecision`.  
  
https://godbolt.org/z/9Pdfre5Yc  
  
`'std::float_denorm_style': warning STL4042: std::float_denorm_style, std::numeric_limits::has_denorm, and std::numeric_limits::has_denorm_loss are deprecated in C++23. You can define _SILENCE_CXX23_DENORM_DEPRECATION_WARNING or _SILENCE_ALL_CXX23_DEPRECATION_WARNINGS to suppress this warning.`
