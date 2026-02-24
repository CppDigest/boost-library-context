# #270 - 64b segments from 32b code: no matching function for call to 'get_rounded_size' [Closed]

> Username: rp42  
> Created at: 2025-09-11 17:36:40 UTC  
> Updated at: 2025-11-11 22:49:47 UTC  
> Closed at: 2025-11-11 22:49:32 UTC  
> Url: https://github.com/boostorg/interprocess/issues/270  

I have some code that shares `boost::interprocess::basic_managed_shared_memory` between a 64b and 32b process as described here: https://stackoverflow.com/a/63224381  
  
With Boost 1.89.0 the 32b process no longer compiles (was using 1.69 previously):  
```detail\segment_manager_helper.hpp(391,14): error : no matching function for call to 'get_rounded_size'```  
  
I think that this is beacuse the template parameter type for `ipcdetail::block_header<unsigned long long>::name_offset<>` is 32b (`std::size_t`), whereas the other sizes are 64b (`uint64_t`).  
  
If I change the template parameter type to be `size_type` it seems to work OK here:  
```  
   template<size_type CharAlign>  
   size_type name_offset() const  
   {  return get_rounded_size(this->name_length_offset()+sizeof(name_len_t), CharAlign);  }  
```  
  
I am compiling using clang-cl on Windows:  
```  
The CXX compiler identification is Clang 19.1.5 with MSVC-like command-line  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-11 22:49:32 UTC  
> Url: https://github.com/boostorg/interprocess/issues/270#issuecomment-3519009896  

Isn't this the same problem that was fixed in #248?   
  
Looks like the fix was not properly merged into Boost 1.89, but the fix is ready for Boost 1.90 (expected to be released during December).

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-11-11 22:49:47 UTC  
> Url: https://github.com/boostorg/interprocess/issues/270#issuecomment-3519011366  

In any case, many thanks for the report.
