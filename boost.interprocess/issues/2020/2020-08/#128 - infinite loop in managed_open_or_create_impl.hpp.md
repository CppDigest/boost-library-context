# #128 - infinite loop in managed_open_or_create_impl.hpp [Closed]

> Username: mmhatami  
> Created at: 2020-08-25 09:57:28 UTC  
> Updated at: 2021-08-24 12:58:05 UTC  
> Closed at: 2021-08-24 12:58:05 UTC  
> Url: https://github.com/boostorg/interprocess/issues/128  

when create a boost::interprocess::managed_shared_memory, if the process killed exactly before set atomic_write32(patomic_word, InitializedSegment) in managed_open_or_create_impl.hpp ,  
  
`detail::atomic_write32(patomic_word, InitializedSegment);`  
  
another process will be caught in an infinite loop in managed_open_or_create_impl.hpp  
  
     boost::uint32_t *patomic_word = static_cast<boost::uint32_t*>(region.get_address());  
     boost::uint32_t value = detail::atomic_read32(patomic_word);  
  
     while(value == InitializingSegment || value == UninitializedSegment){  
        detail::thread_yield();  
        value = detail::atomic_read32(patomic_word);  
     }

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-24 12:57:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/128#issuecomment-904616767  

Thanks for the report. The implementation will add deadlock detection code with configurable macros BOOST_INTERPROCESS_MANAGED_OPEN_OR_CREATE_INITIALIZE_MAX_TRIES/BOOST_INTERPROCESS_MANAGED_OPEN_OR_CREATE_INITIALIZE_TIMEOUT_SEC to handle those situations, as there is no reliable way to detect the other process creating the resource is dead or not.
