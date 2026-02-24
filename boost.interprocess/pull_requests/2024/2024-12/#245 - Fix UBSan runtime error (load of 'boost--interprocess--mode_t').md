# #245 Fix UBSan runtime error (load of 'boost::interprocess::mode_t') [Merged]

> Username: aleden  
> Created at: 2024-12-21 05:09:28 UTC  
> Updated at: 2025-08-25 21:56:49 UTC  
> Merged at: 2025-08-25 21:56:48 UTC  
> Closed at: 2025-08-25 21:56:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/245  

Similar to commit `4e2c06b24211e4983dad95d4b1035d1e0602490d`. `m_mode` is not initialized in `file_wrapper`'s move constructor. UBSan error was triggered on this line in `interprocess/include/boost/interprocess/detail/managed_open_or_create_impl.hpp`:  
  
```cpp  
      if(StoreDevice){                                                                                                                    
         this->DevHolder::get_device() = boost::move(dev);                                                                                                                                                                                                                         
      }                                                                                                                                   
```  
  
My previously submitted and closed PR had a typo.

---
