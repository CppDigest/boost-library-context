# #86 - Unused result warning in executor.hpp [Open]

> Username: gojkovicde  
> Created at: 2019-05-30 16:12:50 UTC  
> Updated at: 2019-05-30 16:12:50 UTC  
> Url: https://github.com/boostorg/process/issues/86  

There's a warning `unused-result` generated in process/detail/posix/executor.hpp  
It refers to 3 calls to `::write` function in `void write_error(const std::error_code & ec, const char * msg)`  
  
Built it with gcc7 on Linux.
