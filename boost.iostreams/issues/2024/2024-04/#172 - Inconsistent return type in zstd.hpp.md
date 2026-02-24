# #172 - Inconsistent return type in zstd.hpp [Open]

> Username: sylvain-loiseau  
> Created at: 2024-04-19 07:45:56 UTC  
> Updated at: 2024-04-19 07:45:56 UTC  
> Url: https://github.com/boostorg/iostreams/issues/172  

In filter/zstd.hpp there is the following code  
  
`int error() const { return error_; }`  
  
I believe the function should return a size_t instead of an int, this causes a compiler warning
