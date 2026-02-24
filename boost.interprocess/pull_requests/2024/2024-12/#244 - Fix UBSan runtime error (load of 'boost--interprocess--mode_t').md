# #244 Fix UBSan runtime error (load of 'boost::interprocess::mode_t') [Closed]

> Username: aleden  
> Created at: 2024-12-21 04:49:05 UTC  
> Updated at: 2024-12-21 05:06:03 UTC  
> Closed at: 2024-12-21 05:06:02 UTC  
> Url: https://github.com/boostorg/interprocess/pull/244  

This is similar to commit `4e2c06b24211e4983dad95d4b1035d1e0602490d` "Fix Issue #64 ("UBSan: runtime error: load of value 4294967295 [...] for type 'boost::interprocess::mode_t'". It was triggered on the following line in `interprocess/include/boost/interprocess/detail/managed_open_or_create_impl.hpp`:  
  
```cpp  
      if(StoreDevice){                                                                                                                    
         this->DevHolder::get_device() = boost::move(dev);                                                                                                                                                                                                                         
      }                                                                                                                                   
```  
  
`file_wrapper`'s move constructor in `include/boost/interprocess/detail/file_wrapper.hpp` simply does not initialize m_mode. This commit initializes it to `read_only`.

---

## Comment 1

> Username: aleden  
> Created_at: 2024-12-21 05:06:02 UTC  
> Url: https://github.com/boostorg/interprocess/pull/244#issuecomment-2558000208  

This commit has a typo. Resubmitting new PR.

---
