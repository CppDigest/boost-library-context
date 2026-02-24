# #130 - infinite loop in managed_open_or_create_impl.hpp if filesize is 0 [Closed]

> Username: cmorty  
> Created at: 2020-09-17 14:38:45 UTC  
> Updated at: 2021-08-24 12:58:06 UTC  
> Closed at: 2021-08-24 12:58:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/130  

Calling `managed_mapped_file(bip::open_only,filename)` on a file with size 0 results in a in infinite loop.  
  
```C++  
            offset_t filesize = 0;  
            spin_wait swait;  
            while(filesize == 0){  
               if(!get_file_size(file_handle_from_mapping_handle(dev.get_mapping_handle()), filesize)){  
                  error_info err = system_error_code();  
                  throw interprocess_exception(err);  
               }  
               swait.yield();  
            }  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-01 14:09:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/130#issuecomment-770884404  

A correctly created managed file can't have zero size. Code is waiting the create process to initialize the managed file (in case of error, file will be truncated to size 1). Which is your use case?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-08-24 12:57:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/130#issuecomment-904616831  

Thanks for the report. The implementation will add deadlock detection code with configurable macros BOOST_INTERPROCESS_MANAGED_OPEN_OR_CREATE_INITIALIZE_MAX_TRIES/BOOST_INTERPROCESS_MANAGED_OPEN_OR_CREATE_INITIALIZE_TIMEOUT_SEC to handle those situations, as there is no reliable way to detect the other process creating the resource is dead or not.
