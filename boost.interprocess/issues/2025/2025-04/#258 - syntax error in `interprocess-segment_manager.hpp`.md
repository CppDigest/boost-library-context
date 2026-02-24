# #258 - syntax error in `interprocess/segment_manager.hpp` [Closed]

> Username: joaquintides  
> Created at: 2025-04-12 11:05:05 UTC  
> Updated at: 2025-04-14 19:13:06 UTC  
> Closed at: 2025-04-14 19:13:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/258  

There's a spurious `template` in   
  
https://github.com/boostorg/interprocess/blob/a22d4986d74cf6228d168b0c0777bbe2f522f4b5/include/boost/interprocess/segment_manager.hpp#L1055  
  
Clang 19 complains with:  
  
```  
.\boost/interprocess/segment_manager.hpp(1055,41): error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
1055 |          hdr = block_header_t::template from_first_header(reinterpret_cast<index_data_t*>((void*)((char*)buffer_ptr + front_space)));  
```
