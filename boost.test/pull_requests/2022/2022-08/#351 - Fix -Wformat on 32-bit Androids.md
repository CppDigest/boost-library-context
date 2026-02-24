# #351 Fix -Wformat on 32-bit Androids [Closed]

> Username: georgthegreat  
> Created at: 2022-08-30 14:17:25 UTC  
> Updated at: 2023-06-09 07:08:42 UTC  
> Closed at: 2023-06-09 07:08:42 UTC  
> Url: https://github.com/boostorg/test/pull/351  

This fixes a bunch of these errors:  
  
```  
In file included from boost/test/include/boost/test/included/unit_test.hpp:23:  
boost/test/include/boost/test/impl/execution_monitor.ipp:429:27: error: format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int') [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2023-06-09 07:08:41 UTC  
> Url: https://github.com/boostorg/test/pull/351#issuecomment-1584083481  

Fixed by: https://github.com/boostorg/test/pull/353

---
